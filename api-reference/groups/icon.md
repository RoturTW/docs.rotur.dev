# Group icon

Upload a group's icon, or fetch it as an image.

## POST `/v2/groups/{tag}/icon`

Upload a new icon. The image is resized to 256×256 and saved as JPEG, replacing any previous icon, and the group's `icon_url` is updated.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the owner or hold `groups.group.edit` or `groups.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `icon` | body (multipart/form-data) | file | Yes | Image file, up to 5 MB and 50 megapixels |

### Example

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/icon" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -F "icon=@icon.png"
```

**Response `200`:**

```json
{
  "message": "Icon uploaded",
  "icon_url": "https://api.rotur.dev/groups/mygroup/icon.jpg"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | There's no file in the `icon` field (`Icon image file is required`) |
| `400` | The file is over 5 MB (`Image too large (max 5MB)`) |
| `400` | The file isn't a readable image or is over 50 megapixels (`Invalid image format`) |
| `403` | You don't have edit access (`You are not authorized to update this group`) |
| `500` | The server couldn't read or encode the image (`Failed to read image`, `Failed to encode icon`) |

## GET `/v2/groups/{tag}/icon.jpg`

Return the group's icon image.

**Auth:** None.

### Example

```http
GET /v2/groups/mygroup/icon.jpg
```

**Response `200`:** the image file.

### Errors

| Status | When |
| --- | --- |
| `404` | The group has no uploaded icon, or doesn't exist (`No icon found`) |
