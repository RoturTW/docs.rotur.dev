# Group Banner

Upload and fetch a group's banner image.

## Upload a Banner

### POST `/v2/groups/{tag}/banner`

**Auth:** required. Token permission: `groups:manage`. You must be the group owner or hold the `groups.group.edit` or `groups.manage` group permission.

The image is resized to **900x300**. JPEG and PNG uploads keep their format; GIFs stay animated. Any previous banner is replaced, and the group's `banner_url` is updated automatically.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Body (multipart/form-data):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `banner` | file | Yes | Image file (max 5MB, JPEG/PNG/GIF) |

**Example request:**

```bash
curl -X POST -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/banner" \  -F "banner=@banner.png"
```

**Example response (200):**

```json
{
  "message": "Banner uploaded",
  "banner_url": "https://api.rotur.dev/groups/mygroup/banner"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Banner image file is required` | No file in the `banner` field |
| 400 | `Image too large (max 5MB)` | File exceeds 5MB |
| 400 | `Invalid image format` | File couldn't be decoded as an image |
| 400 | `Invalid GIF image` | GIF couldn't be processed |
| 403 | `You are not authorized to update this group` | No edit access |
| 404 | `Group not found` | Group doesn't exist |
| 500 | `Failed to save banner` / `Failed to encode banner` | Server-side processing error |

***

## Get a Banner

### GET `/v2/groups/{tag}/banner`

Returns the group's banner image. No authentication needed.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/banner" -o banner.png
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `No banner found` | Group has no banner uploaded |
