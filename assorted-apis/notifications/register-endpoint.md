# Register an endpoint

## POST `/notify/register`

Register a Web Push subscription for your account under a source. If the same device (fingerprint and source) is already registered, its endpoint and keys are replaced instead of adding a duplicate.

**Auth:** Required. Sub-tokens need `account:settings`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `endpoint` | body | string | Yes | The push service URL from the subscription. Must start with `http://` or `https://`, up to 2048 characters |
| `p256dh` | body | string | Yes | The subscription's P-256 public key, base64url without padding. Must decode to 65 bytes starting with `0x04` |
| `auth` | body | string | Yes | The subscription's auth secret, base64url without padding. Must decode to 16 bytes |
| `source` | body | string | Yes | Your app or site name, up to 64 characters, for example `originChats` |
| `fingerprint` | body | string | Yes | A stable device fingerprint, for example a hash of user agent, screen and timezone |

### Example

```http
POST /notify/register
Authorization: Bearer <token>
Content-Type: application/json

{
  "endpoint": "https://push.example.com/deliver/abc123",
  "p256dh": "BASE64URL_P256DH_KEY",
  "auth": "BASE64URL_AUTH_SECRET",
  "source": "originChats",
  "fingerprint": "a1b2c3d4e5f6"
}
```

**Response `200`:**

```json
{
  "message": "endpoint registered",
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6f8a0b2c4",
  "source": "originChats",
  "updated": false
}
```

`device_id` is a 32-character hex string derived from your username, the source and the fingerprint. Store it if you want to delete the device later. `updated` is `true` when an existing registration was replaced.

### Errors

| Status | When |
| --- | --- |
| `400` | `endpoint, p256dh, auth, source, and fingerprint are required` |
| `400` | `source too long (max 64 chars)` |
| `400` | `endpoint URL too long (max 2048 chars)` |
| `400` | `endpoint must be a valid HTTP(S) URL` |
| `400` | `invalid p256dh key` |
| `400` | `invalid auth key` |
| `400` | `maximum number of notification endpoints reached (20)`: you already have 20 devices registered across all sources |
| `500` | `failed to save notification endpoint` |

## Example: subscribe and register from JavaScript

This covers asking for permission, reusing an existing subscription, re-subscribing when the VAPID key changed, and handling API errors.

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
  const headers = { "Content-Type": "application/json", ...options.headers };
  if (authToken) {
    headers.Authorization = authToken.startsWith("Bearer ") ? authToken : `Bearer ${authToken}`;
  }
  const res = await fetch(`https://api.rotur.dev${path}`, {
    ...options,
    headers,
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

  // 2. Fetch the VAPID key and get a subscription
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
  const { device_id, updated } = await registerForNotifications("YOUR_TOKEN", "originChats");
  console.log(updated ? "Updated" : "Registered", device_id);
} catch (e) {
  console.error("Push registration failed:", e.message);
}
```
