# Group Icon

Upload and fetch a group's icon image.

## Upload an Icon

### POST `/v2/groups/{tag}/icon`

**Auth:** required. Token permission: `groups:manage`. You must be the group owner or hold the `groups.group.edit` or `groups.manage` group permission.

The image is resized to **256x256** and saved as JPEG. Any previous icon is replaced, and the group's `icon_url` is updated automatically.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Body (multipart/form-data):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `icon` | file | Yes | Image file (max 5MB) |

**Example request:**

```bash
curl -X POST -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/icon" \  -F "icon=@icon.png"
```

**Example response (200):**

```json
{
  "message": "Icon uploaded",
  "icon_url": "https://api.rotur.dev/groups/mygroup/icon.jpg"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Icon image file is required` | No file in the `icon` field |
| 400 | `Image too large (max 5MB)` | File exceeds 5MB |
| 400 | `Invalid image format` | File couldn't be decoded as an image |
| 403 | `You are not authorized to update this group` | No edit access |
| 404 | `Group not found` | Group doesn't exist |
| 500 | `Failed to read image` / `Failed to encode icon` | Server-side processing error |

***

## Get an Icon

### GET `/v2/groups/{tag}/icon.jpg`

Returns the group's icon as a JPEG. No authentication needed.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/icon.jpg" -o icon.jpg
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `No icon found` | Group has no icon uploaded |
