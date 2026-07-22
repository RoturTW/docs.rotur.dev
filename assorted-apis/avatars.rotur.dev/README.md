# avatars.rotur.dev

Rotur avatars are served from `avatars.rotur.dev`, so you can render a minimal Rotur account UI with only a username.

### How do I get a profile picture?

Take the base URL `https://avatars.rotur.dev/` and add the Rotur username on the end. The username is case-insensitive, the same as everywhere else in Rotur:

* https://avatars.rotur.dev/mist
* https://avatars.rotur.dev/MiSt

You can also pass a user ID (36 characters) instead of a username, and an optional `.gif` suffix is accepted and ignored. `GET` and `HEAD` both work.

### About

* Avatars are stored at 256x256 and served as JPEG, PNG or GIF depending on what was uploaded
* Responses are cached (`ETag` and `Cache-Control: max-age=300`), so repeat requests are cheap
* If the user has not set a profile picture, a default placeholder image is returned

### Animated avatars

Animated GIF avatars only play if the user has a **Plus** subscription or higher. For everyone else the first frame is served as a still PNG. Add `?no_animate=1` to force a still image regardless of tier.

### Query Parameters

| Parameter | Description |
|---|---|
| `s` | Size in pixels, from 1 to 256. Default is the stored size (256). Example: `?s=128` |
| `radius` | Corner radius in pixels (a `px` suffix is accepted). `?radius=128` gives a circle. Capped at 128 for GIFs and at half the image height for stills. Rounded stills are returned as PNG. |
| `no_animate` | Set to `1` to always get a still image |

### Other endpoints on this host

| Endpoint | Description |
|---|---|
| [`/.banners/:username`](.banners.md) | The user's profile banner |
| [`/.overlay/:username`](overlay.md) | The user's equipped avatar overlay |
| [`POST /rotur-upload-pfp`, `POST /rotur-upload-banner`](upload.md) | Upload a new avatar or banner |

The same routes are also available with v2-style paths: `/v2/avatars/:username`, `/v2/avatars/:username/banner`, `/v2/avatars/:username/overlay`, `/v2/avatars/upload/pfp` and `/v2/avatars/upload/banner`.
