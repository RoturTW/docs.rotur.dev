# VAPID keys

## GET `/notify/vapid`

Get the server's VAPID public key. Browsers need it as the `applicationServerKey` when subscribing to push.

**Auth:** None.

The key pair is generated once and stored on the server, so it stays the same across restarts unless the server's key file is replaced.

### Example

```http
GET /notify/vapid
```

**Response `200`:**

```json
{
  "public_key": "BEs9Xb2...",
  "subject": "mailto:admin@rotur.dev"
}
```

| Field | Type | Description |
| --- | --- | --- |
| `public_key` | string | Uncompressed P-256 public key, base64url-encoded |
| `subject` | string | The VAPID subject the server signs pushes with, usually a `mailto:` URI |

## Use it with the Push API

Pass `public_key` to `pushManager.subscribe` as the `applicationServerKey`:

```js
const res = await fetch("https://api.rotur.dev/notify/vapid");
const { public_key } = await res.json();

const subscription = await registration.pushManager.subscribe({
  userVisibleOnly: true,
  applicationServerKey: public_key,
});
```

Then register the subscription with [POST `/notify/register`](register-endpoint.md).
