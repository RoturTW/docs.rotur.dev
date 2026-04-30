# Register an Endpoint

### POST `/notify/register`

Registers a push notification endpoint for the authenticated user. If the device (identified by the fingerprint + source combination) already exists, its endpoint URL is updated instead of creating a duplicate.

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `endpoint` | string | Yes | The push URL to deliver notifications to (must be `http://` or `https://`) |
| `p256dh` | string | Yes | Base64url-encoded P-256 public key for the push subscription |
| `auth` | string | Yes | Base64url-encoded authentication secret for the push subscription |
| `source` | string | Yes | The application/site name (max 64 chars, e.g. `originChats`) |
| `fingerprint` | string | Yes | A stable device fingerprint (e.g. hash of user-agent + screen + timezone) |

**Request:**

```json
{
  "endpoint": "https://push.example.com/deliver/abc123",
  "p256dh": "BASE64URL_P256DH_KEY",
  "auth": "BASE64URL_AUTH_SECRET",
  "source": "originChats",
  "fingerprint": "a1b2c3d4e5f6"
}
```

**Response (200):**

```json
{
  "message": "endpoint registered",
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6",
  "source": "originChats",
  "updated": false
}
```

The `device_id` is server-generated and deterministic. Persist it client-side so you can check registration status or delete the device later. The `updated` field is `true` when an existing endpoint was updated rather than newly created.

Users are limited to **20 registered endpoints**.

## Example: Subscribe & Register from JavaScript

This example covers the full lifecycle: permission handling, duplicate subscription detection, broken subscription recovery, and API error handling.

```js
// --- Helpers ---

function urlBase64ToUint8Array(base64Url) {
  const padding = "=".repeat((4 - (base64Url.length % 4)) % 4);
  const base64 = (base64Url + padding).replace(/-/g, "+").replace(/_/g, "/");
  return Uint8Array.from(atob(base64), c => c.charCodeAt(0));
}

function arrayBufferToBase64Url(buffer) {
  return btoa(String.fromCharCode(...new Uint8Array(buffer)))
    .replace(/\+/g, "-").replace(/\//g, "_").replace(/=+$/, "");
}

async function getFingerprint() {
  const raw = navigator.platform + navigator.language + screen.colorDepth
    + Intl.DateTimeFormat().resolvedOptions().timeZone;
  const hash = await crypto.subtle.digest("SHA-256", new TextEncoder().encode(raw));
  return Array.from(new Uint8Array(hash), b => b.toString(16).padStart(2, "0")).join("");
}

async function api(path, authToken, options = {}) {
  const res = await fetch(`https://api.rotur.dev${path}?auth=${authToken}`, {
    ...options,
    headers: { "Content-Type": "application/json", ...options.headers },
  });
  if (!res.ok) {
    const { error } = await res.json().catch(() => ({}));
    throw new Error(error || `API ${res.status}`);
  }
  return res.json();
}

// --- Main ---

async function registerForNotifications(authToken, source) {
  // 1. Request notification permission
  if (Notification.permission === "default") {
    await Notification.requestPermission();
  }
  if (Notification.permission !== "granted") {
    throw new Error("Notification permission denied");
  }

  const registration = await navigator.serviceWorker.ready;

  // 2. Fetch VAPID key & build a fresh subscription
  const { public_key } = await api("/notify/vapid", authToken);
  const appKey = urlBase64ToUint8Array(public_key);

  let subscription = await registration.pushManager.getSubscription();

  // Re-subscribe if the existing subscription uses a different VAPID key
  // (e.g. after a server key rotation) or is otherwise broken
  if (subscription && subscription.options?.applicationServerKey) {
    const oldKey = new Uint8Array(subscription.options.applicationServerKey);
    if (oldKey.join() !== appKey.join()) {
      await subscription.unsubscribe();
      subscription = null;
    }
  }

  if (!subscription) {
    subscription = await registration.pushManager.subscribe({
      userVisibleOnly: true,
      applicationServerKey: appKey,
    });
  }

  // 3. Register with Rotur
  const fingerprint = await getFingerprint();

  return await api("/notify/register", authToken, {
    method: "POST",
    body: JSON.stringify({
      endpoint: subscription.endpoint,
      p256dh: arrayBufferToBase64Url(subscription.getKey("p256dh")),
      auth: arrayBufferToBase64Url(subscription.getKey("auth")),
      source,
      fingerprint,
    }),
  });
}

// Usage
try {
  const { device_id, updated } = await registerForNotifications("your_auth_token", "source (eg. originChats)");
  console.log(updated ? "Updated" : "Registered", device_id);
} catch (e) {
  console.error("Push registration failed:", e.message);
}
```
