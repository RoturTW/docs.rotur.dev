# Accessing OFSF Storage

OFSF (originFS File System) is the cloud file storage used by originOS. This page shows how to download everything you have stored in one request. To read or write single files, use the [`/files` endpoints](../claw/api-endpoints/files.md).

> **Base URL:** `https://api.rotur.dev`
> **Auth:** `Authorization: Bearer <token>` with your account token (`key` on your account object). The legacy `auth` query parameter is also accepted. Sub-tokens need `files:view`.

## GET `/read-files`

Returns every file and folder you have stored, including file contents. `GET /files/entries` and `GET /v2/files/entries` return the same data.

**Auth:** Required. Sub-tokens need `files:view`.

### Example

```bash
curl -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/read-files"
```

```javascript
const response = await fetch("https://api.rotur.dev/read-files", {
  headers: { Authorization: `Bearer ${token}` },
});
const ofsfData = await response.json();
```

**Response `200`:**

The body is JSON sent with `Content-Type: application/octet-stream`, so parse it yourself (`response.json()` works). It is a flat array of OFSF entries: each file or folder adds 14 consecutive values. If you have no files, the body is `null` or `[]`. The response can be large, depending on how much you store.

### Errors

| Status | When |
| --- | --- |
| `403` | The token is missing or invalid, the account is banned, or a sub-token lacks `files:view` |
| `500` | The server could not read or serialize your files |

## Smaller requests

If you don't need everything at once, these endpoints return less. They use the same authentication.

| Endpoint | Returns |
| --- | --- |
| `GET /files/index` | The file index. Contents of files over 50 KB are left out. |
| `GET /files/usage` | Your storage usage |
| `GET /files/by-path/{path}` | One file by its originFS path |
| `GET /files/by-uuid?uuid=...` | One file by UUID |

See [/files](../claw/api-endpoints/files.md) for details.

## Storage limits

Your storage limit depends on your [subscription tier](subscriptions.md) and is shown in the `max_size` key on your account.

| Tier | Storage |
| --- | --- |
| Free | 5 MB |
| Lite | 25 MB |
| Plus | 100 MB |
| Pro | 1 GB |

{% hint style="warning" %}
Your token gives full access to your account. Don't embed it in code that other people can read.
{% endhint %}

{% hint style="info" %}
This HTTP endpoint replaces the old websocket-based system and the legacy `originfiles.mistium.com` host. Use `api.rotur.dev` for new integrations.
{% endhint %}
