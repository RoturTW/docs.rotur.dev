# avatars.rotur.dev

`avatars.rotur.dev` serves Rotur profile pictures, banners and overlays by username, so you can show a user's avatar with nothing but their name.

> **Base URL:** `https://avatars.rotur.dev`
>
> **Auth:** None for reading images. [Uploading and removing](upload.md) takes your main account token.

## Endpoints

| Method | Endpoint | Description |
| --- | --- | --- |
| GET | `/:username` | The user's profile picture (this page) |
| GET | [`/.banners/:username`](.banners.md) | The user's profile banner |
| GET | [`/.overlay/:username`](overlay.md) | The avatar overlay the user has equipped |
| GET | `/.backgrounds/:username` (below) | The profile background the user has equipped |
| GET | `/.videos/:username` (below) | The user's own uploaded background video |
| POST | [`/rotur-upload-pfp`](upload.md) | Upload a profile picture |
| POST | [`/rotur-upload-banner`](upload.md) | Upload a banner |
| POST | [`/rotur-upload-overlay`](upload.md) | Upload a custom overlay |
| POST | [`/rotur-upload-background`](upload.md) | Upload a background video. `/rotur-upload-profile-video` is the same |
| DELETE | [`/rotur-remove-banner`](upload.md) | Remove your banner |
| DELETE | [`/rotur-remove-overlay`](upload.md) | Remove your custom overlay |
| DELETE | [`/rotur-remove-background`](upload.md) | Remove your background video. `/rotur-remove-profile-video` is the same |

The same routes are also available with v2 paths: `/v2/avatars/:username`, `/v2/avatars/:username/banner`, `/v2/avatars/:username/overlay`, `/v2/avatars/:username/background`, `/v2/avatars/:username/profile-video`, `/v2/avatars/upload/pfp`, `/v2/avatars/upload/banner`, `/v2/avatars/upload/overlay`, `/v2/avatars/upload/background` (or `/upload/profile-video`), and `DELETE` on `/v2/avatars/banner`, `/v2/avatars/overlay` and `/v2/avatars/background` (or `/profile-video`). The `/v2/avatars` upload and remove routes also work on `https://api.rotur.dev`, where a rotur.dev browser session counts as your token.

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

## GET `/.backgrounds/:username`

Returns the profile background the user has equipped. Also accepts `HEAD`.

**Auth:** None.

If the user has equipped their own uploaded video, you get the MP4 directly. If they have equipped a background from the [cosmetics shop](../economy/cosmetics/README.md), you get a `307` redirect to its file on `https://api.rotur.dev/cosmetics/backgrounds/`. Videos are served as `video/mp4` with `Cache-Control: private, no-store`, and range requests work, so you can use the URL in a `<video>` element.

`GET /.videos/:username` returns only the user's own uploaded video, whatever they have equipped.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The username (case-insensitive) |

### Errors

| Status | When |
| --- | --- |
| `404` | No such user, or nothing to serve: no background equipped, or for `/.videos`, no uploaded video or no longer the perk that allows one. The body is empty |
