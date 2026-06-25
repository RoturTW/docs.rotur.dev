# Group Banner

## Upload a Banner

### POST `/groups/{tag}/banner`

Upload an image to use as the group banner. **Owner only.**

The image is automatically resized to **900×300**.

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
| `banner` | file | Yes | Image file (max 5MB, JPEG/PNG/GIF) |

The uploaded image is decoded, resized to 900×300 using Lanczos resampling, and saved in its original format. GIF uploads are resized frame-by-frame and preserved as GIF. PNG uploads are saved as PNG. All others are saved as JPEG (quality 85). Any previous banner file is removed.

**Response (200):**

```json
{
  "message": "Banner uploaded",
  "banner_url": "https://api.rotur.dev/groups/mygroup/banner"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Banner image file is required` | No file in `banner` field |
| 400 | `Image too large (max 5MB)` | File exceeds 5MB |
| 400 | `Invalid image format` | File couldn't be decoded as an image |
| 400 | `Invalid GIF image` | GIF couldn't be resized |
| 403 | `You are not authorized to update this group` | Not the group owner |
| 404 | `Group not found` | Group doesn't exist |
| 500 | `Failed to read image` | Error reading uploaded file |
| 500 | `Failed to save banner` | Filesystem error |
| 500 | `Failed to encode banner` | Encoding error |

---

## Get a Banner

### GET `/groups/{tag}/banner`

Returns the group's banner image file. No authentication required.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Response (200):** Image file (`image/jpeg`, `image/png`, or `image/gif` depending on what was uploaded).

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `No banner found` | Group has no banner uploaded |
