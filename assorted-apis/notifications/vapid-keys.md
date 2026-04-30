# VAPID Keys

### GET `/notify/vapid`

Returns the server's VAPID public key used for web push authentication. Clients need this key to subscribe to push notifications via the Web Push API.

This endpoint does **not** require authentication.

**Response (200):**

```json
{
  "public_key": "BEs9Xb2...",
  "subject": "mailto:admin@rotur.dev"
}
```

| Field | Type | Description |
| --- | --- | --- |
| `public_key` | string | Uncompressed P-256 public key encoded as standard Base64 |
| `subject` | string | The VAPID subject (typically a `mailto:` URI) |

## Usage with Web Push

Pass `public_key` to `registration.pushManager.subscribe` as the `applicationServerKey`:

```js
const res = await fetch("https://api.rotur.dev/notify/vapid");
const { public_key } = await res.json();

const subscription = await registration.pushManager.subscribe({
  userVisibleOnly: true,
  applicationServerKey: public_key,
});
```

Then register the subscription endpoint with [POST `/notify/register`](register-endpoint.md).
