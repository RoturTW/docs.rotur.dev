# Group banner

Upload a group's banner, or fetch it as an image.

## POST `/v2/groups/{tag}/banner`

Upload a new banner. The image is resized to 900×300, replacing any previous banner, and the group's `banner_url` is updated. The saved format follows the file part's `Content-Type`: `image/gif` stays an animated GIF, `image/png` stays PNG, and anything else is saved as JPEG.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the owner or hold `groups.group.edit` or `groups.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `banner` | body (multipart/form-data) | file | Yes | Image file, up to 5 MB and 50 megapixels |

### Example

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/banner" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "banner=@banner.png"
```

**Response `200`:**

```json
{
  "message": "Banner uploaded",
  "banner_url": "https://api.rotur.dev/groups/mygroup/banner"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | There's no file in the `banner` field (`Banner image file is required`) |
| `400` | The file is over 5 MB (`Image too large (max 5MB)`) |
| `400` | The file isn't a readable image or is over 50 megapixels (`Invalid image format`) |
| `400` | The GIF couldn't be resized (`Invalid GIF image`) |
| `403` | You don't have edit access (`You are not authorized to update this group`) |
| `500` | The server couldn't read, save, or encode the image (`Failed to read image`, `Failed to save banner`, `Failed to encode banner`) |

## GET `/v2/groups/{tag}/banner`

Return the group's banner image.

**Auth:** None.

### Example

```http
GET /v2/groups/mygroup/banner
```

**Response `200`:** the image file, as JPEG, PNG, or GIF.

### Errors

| Status | When |
| --- | --- |
| `404` | The group has no uploaded banner, or doesn't exist (`No banner found`) |
