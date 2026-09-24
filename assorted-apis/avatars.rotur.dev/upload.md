# Uploading

Upload a new profile picture or banner for your account.

> **Base URL:** `https://avatars.rotur.dev`
>
> **Auth:** Your main account token in the `token` body field. Sub-tokens and the `Authorization` header are not accepted.

| Method | Endpoint | v2 path | Description |
| --- | --- | --- | --- |
| POST | `/rotur-upload-pfp` | `/v2/avatars/upload/pfp` | Upload a profile picture |
| POST | `/rotur-upload-banner` | `/v2/avatars/upload/banner` | Upload a banner |

Both endpoints take the same body.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `image` | body | string | Yes | The image as a Base64 data URI, for example `data:image/png;base64,...` |
| `token` | body | string | Yes | Your main account token |

### Limits and processing

* The decoded image can be up to 10 MB and 50 million pixels.
* Profile pictures are resized to 256×256. GIFs stay animated; everything else is converted to JPEG.
* Banners are resized to 900×300 and kept as GIF, PNG or JPEG. A fully transparent banner is rejected.
* Anyone can upload an animated GIF, but it only plays for accounts with a Plus subscription or higher.

## POST `/rotur-upload-pfp`

Replaces your profile picture.

**Auth:** Required. Main token in the `token` body field.

### Example

```http
POST https://avatars.rotur.dev/rotur-upload-pfp
Content-Type: application/json

{
  "image": "data:image/png;base64,iVBORw0KGgo...",
  "token": "YOUR_TOKEN"
}
```

**Response `200`:**

```json
{
  "status": "Success",
  "message": "Profile picture uploaded successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The body is not valid JSON (`Invalid JSON data`) |
| `400` | `image` is missing (`Missing image`) |
| `400` | The image is not a valid data URI, cannot be decoded, or is too large |
| `403` | `token` is not a valid main account token (`Invalid token`) |

## POST `/rotur-upload-banner`

Replaces your banner.

**Auth:** Required. Main token in the `token` body field.

{% hint style="warning" %}
Banners cost a one-time 30-credit unlock, charged on your first successful upload. Pro and higher subscribers upload banners for free, and accounts that already had a banner are already unlocked.
{% endhint %}

### Example

```http
POST https://avatars.rotur.dev/rotur-upload-banner
Content-Type: application/json

{
  "image": "data:image/png;base64,iVBORw0KGgo...",
  "token": "YOUR_TOKEN"
}
```

**Response `200`:**

```json
{
  "status": "Success",
  "message": "Banner uploaded successfully",
  "banner": "https://avatars.rotur.dev/.banners/mist?v=1715512345678"
}
```

`banner` is your banner URL with a `v` cache-busting parameter.

### Errors

| Status | When |
| --- | --- |
| `400` | The body is not valid JSON (`Invalid JSON data`) |
| `400` | `image` is missing (`Missing image`) |
| `400` | The image is not a valid data URI, cannot be decoded, is too large, or is fully transparent |
| `403` | `token` is not a valid main account token (`Invalid token`) |
| `403` | Banners are not unlocked and you have fewer than 30 credits |
