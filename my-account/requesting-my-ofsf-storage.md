# Accessing OFSF Storage

OFSF (OriginFS File System) is the cloud file storage used by originOS. You can download all your files with one simple request.

## Authentication

All requests need your account token (`userObject.key`). Pass it as the `auth` query parameter, or as a `Bearer` token in the `Authorization` header.

## Endpoint

### Get All Your Files

**URL:** `https://api.rotur.dev/read-files`

**Method:** `GET`

**Parameters:**

- `auth` (required): your account token

### Request Example

```bash
curl "https://api.rotur.dev/read-files?auth=YOUR_USER_TOKEN"
```

```javascript
const userToken = "your_user_token_here";
const response = await fetch(`https://api.rotur.dev/read-files?auth=${userToken}`);
const ofsfData = await response.json();
```

### Response

The endpoint returns your complete OFSF file index as JSON, including file contents, metadata, and folder structure. The exact shape depends on what you have stored.

**Success Response:**
- **Status Code:** 200 OK
- **Body:** Complete OFSF file system data (served as a binary JSON stream)

**Error Responses:**
- **401 / 403:** Invalid or missing authentication token
- **500 Internal Server Error:** Server-side processing error

## Finer-Grained Access

If you don't want everything at once, the `/files` endpoints let you work with individual files:

- `GET /files/index` returns the file index
- `GET /files/usage` returns your storage usage
- `GET /files/by-path/{path}` returns a single file by path
- `GET /files/by-uuid?uuid=...` returns a single file by UUID

All of these take the same `auth` parameter.

## Usage Notes

- The `/read-files` response can be large depending on how much you have stored.
- Your storage limit depends on your subscription tier: 5 MB on Free, up to 1 GB on Pro.
- Keep your token secure. Never expose it in client-side code.

{% hint style="warning" %}
This HTTP endpoint replaces the old WebSocket-based system and the legacy `originfiles.mistium.com` host. Use `api.rotur.dev` for all new integrations.
{% endhint %}
