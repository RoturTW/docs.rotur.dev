# Uploading

Upload a new profile picture, banner, custom overlay or background video for your account, or remove one.

> **Base URL:** `https://avatars.rotur.dev`
>
> **Auth:** Your main account token, in the `token` body field or the `Authorization: Bearer <token>` header. Sub-tokens are not accepted.

| Method | Endpoint | v2 path | Description |
| --- | --- | --- | --- |
| POST | `/rotur-upload-pfp` | `/v2/avatars/upload/pfp` | Upload a profile picture |
| POST | `/rotur-upload-banner` | `/v2/avatars/upload/banner` | Upload a banner |
| POST | `/rotur-upload-overlay` | `/v2/avatars/upload/overlay` | Upload a custom overlay |
| POST | `/rotur-upload-background` | `/v2/avatars/upload/background` | Upload a background video |
| DELETE | `/rotur-remove-banner` | `/v2/avatars/banner` | Remove your banner |
| DELETE | `/rotur-remove-overlay` | `/v2/avatars/overlay` | Remove your custom overlay |
| DELETE | `/rotur-remove-background` | `/v2/avatars/background` | Remove your background video |

`/rotur-upload-profile-video` and `/rotur-remove-profile-video` (v2: `upload/profile-video` and `profile-video`) are older names for the background routes.

The profile picture and banner endpoints take the same JSON body.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `image` | body | string | Yes | The image as a Base64 data URI, for example `data:image/png;base64,...` |
| `token` | body | string | No | Your main account token. Leave it out if you send the token in the `Authorization` header |

### Limits and processing

* The decoded image can be up to 10 MB and 50 million pixels.
* Profile pictures are resized to 256×256. GIFs stay animated; everything else is converted to JPEG.
* Banners are resized to 900×300 and kept as GIF, PNG or JPEG. A fully transparent banner is rejected.
* Anyone can upload an animated GIF, but it only plays for accounts with a Plus subscription or higher.

## POST `/rotur-upload-pfp`

Replaces your profile picture.

**Auth:** Required. Main token in the `token` body field or the `Authorization` header.

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

**Auth:** Required. Main token in the `token` body field or the `Authorization` header.

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

## POST `/rotur-upload-overlay`

Uploads a custom overlay GIF and equips it as your `custom_overlay` [cosmetic](../economy/cosmetics/README.md#custom-cosmetics). It is then served at `/.overlay/<username>`.

**Auth:** Required. Main token in the `token` form field or the `Authorization` header. Needs Plus or higher, or an equipped pet that grants custom overlays.

The body is `multipart/form-data`, not JSON.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `overlay` | form | file | Yes | A GIF, up to 10 MiB and 1024×1024 |
| `token` | form | string | No | Your main account token, if not in the header |

**Response `200`:**

```json
{
  "status": "Success",
  "overlay": "https://avatars.rotur.dev/.overlay/mist"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Missing overlay`, or the file isn't a valid GIF up to 1024×1024 |
| `403` | `Invalid token` |
| `403` | Your account doesn't have custom overlays |
| `409` | The file is identical to an overlay in the cosmetics shop. Custom uploads must be your own content |
| `413` | The body isn't multipart, or the file is empty or over 10 MiB |

## POST `/rotur-upload-background`

Uploads a background video and equips it as your `custom_background` [cosmetic](../economy/cosmetics/README.md#custom-cosmetics). It is then served at `/.backgrounds/<username>` and `/.videos/<username>`.

**Auth:** Required. Main token in the `token` form field or the `Authorization` header. Needs Pro or higher, or an equipped pet that grants custom backgrounds.

The body is `multipart/form-data`, not JSON.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `video` | form | file | Yes | A video file, up to 50 MiB |
| `token` | form | string | No | Your main account token, if not in the header |

The video is stored as an MP4 without sound, at most 1920×1080, 30 fps and 30 seconds long. Videos already within those limits in H.264 are kept as they are; anything else is re-encoded and cut to 30 seconds.

**Response `200`:**

```json
{
  "status": "Success",
  "background": "https://avatars.rotur.dev/.backgrounds/mist",
  "profile_video": "https://avatars.rotur.dev/.backgrounds/mist"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Missing video`, or the file isn't a valid video |
| `403` | `Invalid token` |
| `403` | Your account doesn't have custom backgrounds |
| `408` | Processing took longer than 2 minutes |
| `413` | The body isn't multipart, or the file is empty or over 50 MiB |
| `503` | Too many videos are being processed. Try again shortly |

## Removing uploads

`DELETE /rotur-remove-banner`, `DELETE /rotur-remove-overlay` and `DELETE /rotur-remove-background` remove your banner, custom overlay or background video. Removing an overlay or background also unequips it. Removing your banner doesn't undo the banner unlock, so you can upload a new one later without paying again.

**Auth:** Required. Main token in a JSON body (`{"token": "..."}`) or the `Authorization` header.

**Response `200`:**

```json
{
  "status": "Success",
  "message": "Banner removed"
}
```

The message is `Banner removed`, `Custom overlay removed` or `Profile video removed`. Errors are `400` (`Invalid JSON data`) and `403` (`Invalid token`).
