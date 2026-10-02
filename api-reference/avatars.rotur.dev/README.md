# avatars.rotur.dev

`avatars.rotur.dev` serves Rotur profile pictures, banners and overlays by username, so you can show a user's avatar with nothing but their name.

> **Base URL:** `https://avatars.rotur.dev`
>
> **Auth:** None for reading images. [Uploading](upload.md) takes your main account token in the request body.

## Endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/:username` | The user's profile picture (this page) |
| GET | [`/.banners/:username`](.banners.md) | The user's profile banner |
| GET | [`/.overlay/:username`](overlay.md) | The avatar overlay the user has equipped |
| POST | [`/rotur-upload-pfp`](upload.md) | Upload a profile picture |
| POST | [`/rotur-upload-banner`](upload.md) | Upload a banner |

The same routes are also available with v2 paths: `/v2/avatars/:username`, `/v2/avatars/:username/banner`, `/v2/avatars/:username/overlay`, `/v2/avatars/upload/pfp` and `/v2/avatars/upload/banner`.

## GET `/:username`

Returns the user's profile picture. Also accepts `HEAD`.

**Auth:** None.

The username is case-insensitive, so these return the same image:

* https://avatars.rotur.dev/mist
* https://avatars.rotur.dev/MiSt

You can pass a 36-character user ID instead of a username. A `.gif` suffix is accepted and ignored.

Avatars are stored at 256×256. An uploaded GIF is served as a GIF; any other upload is served as a JPEG. If the user has no profile picture, you get the default placeholder image. Responses carry an `ETag` and `Cache-Control: public, max-age=300, must-revalidate`, and a matching `If-None-Match` returns `304`.

Animated GIF avatars only play for users with a Plus subscription or higher. For everyone else, the first frame is served as a still PNG.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | A username or 36-character user ID |
| `s` | query | integer | No | Size in pixels, from 1 to 256. Other values are ignored. Example: `?s=128` |
| `radius` | query | integer | No | Corner radius in pixels. A `px` suffix is accepted. `?radius=128` gives a circle. Capped at 128 for GIFs and at half the image height for stills. Rounded stills are returned as PNG. |
| `no_animate` | query | string | No | `1` always returns a still image |

### Example

```http
GET /mist?s=64&radius=32
```

**Response `200`:** the image bytes.

### Errors

| Status | When |
| --- | --- |
| `400` | A 36-character ID was given and no user has it (`User not found`) |
