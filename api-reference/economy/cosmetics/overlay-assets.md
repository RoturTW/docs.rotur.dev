# Cosmetic assets

Fetch the raw files for catalog cosmetics: overlay GIFs and background videos. The `raw_url` returned by the [profile cosmetics endpoints](profile-cosmetics.md) points here for catalog items.

Errors from these endpoints are bare status codes with no JSON body.

## GET `/cosmetics/overlays/:file`

Fetch an overlay GIF, for example to preview it in a shop.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `file` | path | string | Yes | The cosmetic ID followed by `.gif` |

### Example

```http
GET /cosmetics/overlays/cat_ears.gif
```

**Response `200`:** the GIF file.

### Errors

| Status | When |
| --- | --- |
| `400` | The file name is missing, contains `..`, is not a clean path, or does not end in `.gif` |
| `404` | No overlay with that name |

## GET `/cosmetics/backgrounds/:file`

Fetch a background video.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `file` | path | string | Yes | The cosmetic ID followed by `.mp4` |

### Example

```http
GET /cosmetics/backgrounds/starfield.mp4
```

**Response `200`:** the video, served as `video/mp4` with `Cache-Control: public, max-age=86400`.

### Errors

| Status | When |
| --- | --- |
| `400` | The file name is missing, contains `..`, is not a clean path, or does not end in `.mp4` |
| `404` | No background with that name |
