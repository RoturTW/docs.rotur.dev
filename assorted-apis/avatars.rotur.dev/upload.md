# Uploading

Upload a new profile picture or banner for your account.

| Endpoint | Method | Description |
|---|---|---|
| `https://avatars.rotur.dev/rotur-upload-pfp` | POST | Upload a profile picture |
| `https://avatars.rotur.dev/rotur-upload-banner` | POST | Upload a banner |

**Request Body (JSON):**

| Field | Type | Required | Description |
|---|---|---|---|
| `image` | string | Yes | The image as a base64 data URI (e.g. `data:image/png;base64,...`) |
| `token` | string | Yes | Your account token |

**Limits and processing:**

* Maximum decoded size is 10 MB, and at most 50 megapixels
* Profile pictures are resized to 256x256. GIFs stay animated, everything else becomes JPEG
* Banners are resized to 900x300 and kept as GIF, PNG or JPEG

{% hint style="info" %}
Animated uploads are accepted from anyone, but the animation only plays for viewers of accounts with the required subscription tier (Plus for avatars, Pro for banners).
{% endhint %}

**Example request:**

```http
POST https://avatars.rotur.dev/rotur-upload-pfp
Content-Type: application/json

{
  "image": "data:image/png;base64,iVBORw0KGgo...",
  "token": "YOUR_TOKEN"
}
```

**Example response (200):**

```json
{
  "status": "Success",
  "message": "Profile picture uploaded successfully"
}
```

The banner endpoint responds with `"Banner uploaded successfully"`.

**Common errors:**

| Status | Error |
|---|---|
| `400` | `Invalid JSON data` |
| `400` | `Missing image` |
| `400` | `invalid image format` / `invalid image data` / `image too large` |
| `403` | `Invalid token` |
