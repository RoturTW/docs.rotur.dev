# Group Icon

## Upload an Icon

### POST `/groups/{tag}/icon`

Upload an image to use as the group icon. **Owner only.** The image is automatically resized to **256×256 JPEG**.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Body (multipart/form-data):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `icon` | file | Yes | Image file (max 5MB, any common format) |

The uploaded image is decoded, resized to 256×256 using Lanczos resampling, and saved as a JPEG (quality 85). Any previous icon file is removed.

**Response (200):**

```json
{
  "message": "Icon uploaded",
  "icon_url": "https://api.rotur.dev/groups/mygroup/icon.jpg"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Icon image file is required` | No file in `icon` field |
| 400 | `Image too large (max 5MB)` | File exceeds 5MB |
| 400 | `Invalid image format` | File couldn't be decoded as an image |
| 403 | `You are not authorized to update this group` | Not the group owner |
| 404 | `Group not found` | Group doesn't exist |
| 500 | `Failed to save icon` | Filesystem error |
| 500 | `Failed to encode icon` | JPEG encoding error |

---

## Get an Icon

### GET `/groups/{tag}/icon.jpg`

Returns the group's icon as a JPEG file. No authentication required.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Response (200):** JPEG image file with `Content-Type: image/jpeg`.

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `No icon found` | Group has no icon uploaded |
