# /emojis

`/emojis` is the Plus+ emoji system for Rotur accounts. It lets users upload custom emoji images, keep them under their account, and add other users' emojis to their collection.

All endpoint path identifiers use numeric emoji IDs. Emoji binary files are still stored by SHA-256 hash on disk.
Authentication should be sent using the `Authorization` header whenever it is required.

## Storage model

* Uploaded and added emojis are tracked per user in `emojis.json` under that user's userdata folder.
* Binary emoji files are stored at `./rotur/emojis/<sha256-hash>`.
* Each saved emoji has a numeric `id` field in `emojis.json` (derived from the hash).

Hash is lowercased by the service and must be 64 hex characters.

## Limits

The `/emojis` collection limit applies to **saved emojis total** (uploaded + added), deduplicated by hash.

* Plus: 50 saved emojis
* Pro: 500 saved emojis
* Free/Lite: no access

If you have no Plus+ subscription, all write endpoints return `403` with:

```json
{ "error": "You have no subscription" }
```

`GET /emojis` returns `404` if your account is not Plus+.
`GET /emojis/:emojiId` returns `404` if the emoji owner is not Plus+.

## Endpoints

### GET `/emojis/:emojiId`

Returns the raw emoji image for an uploaded emoji. This is unauthenticated.

### Parameters

| Parameter | Required | Description |
| --- | --- | --- |
| emojiId | Yes | Numeric emoji id |

### Example

```bash
curl "https://api.rotur.dev/emojis/12345"
```

### GET `/emojis`

Lists your saved emojis.

**Auth required.** Returns `404` if your tier is below Plus.

### Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/emojis"
```

### Response

```json
{
  "uploaded": [
    { "id": 12345, "hash": "...", "name": "my smiley", "owner": "12345", "url": "https://api.rotur.dev/emojis/12345" }
  ],
  "added": [
    { "id": 12346, "hash": "...", "name": "funny", "owner": "67890", "url": "https://api.rotur.dev/emojis/12346" }
  ]
}
```

### POST `/emojis`

Uploads a new emoji and adds it to your uploaded list.

### Parameters

| Parameter | Required | Description |
| --- | --- | --- |
| Authorization | Yes | `Bearer YOUR_AUTH_KEY` in request header |
| image | Yes | A data URI containing PNG/GIF/JPEG binary |
| name | Yes | Display name (max 80 chars) |

### Example

```bash
curl -X POST -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/emojis" \
  -H "Content-Type: application/json" \
  -d '{ "name":"sparkles", "image":"data:image/png;base64,..." }'
```

### Response

```json
{
  "status": "uploaded",
  "hash": "a3b9...9c1f",
  "id": 12345,
  "name": "sparkles",
  "content_type": "image/png",
  "url": "https://api.rotur.dev/emojis/12345"
}
```

### POST `/emojis/:emojiId/add`

Adds another user's emoji to your own account. This may optionally rename it.

### Parameters

| Parameter | Required | Description |
| --- | --- | --- |
| Authorization | Yes | `Bearer YOUR_AUTH_KEY` in request header |
| emojiId | Yes | Numeric id of the source emoji |
| name | No | Optional replacement name (max 80 chars) |

### Example

```bash
curl -X POST -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/emojis/12345/add" \
  -H "Content-Type: application/json" \
  -d '{ "name":"favorite mist emoji" }'
```

### POST `/emojis/:emojiId/unsave`

Removes an emoji from your **added** list.

Uploaded emojis cannot be unsaved.

### Example

```bash
curl -X POST -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/emojis/12345/unsave"
```

### DELETE `/emojis/:emojiId`

Deletes an uploaded emoji from your own uploaded list.

It also removes that hash from every user's saved list and deletes the backing file.

Only the uploader can delete.

### Example

```bash
curl -X DELETE -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/emojis/12345"
```

### DELETE `/admin/emojis/:id`

Network admins can remove an emoji globally by numeric id.

### Parameters

| Parameter | Required | Description |
| --- | --- | --- |
| Authorization | Yes | `Bearer YOUR_AUTH_KEY` in request header |
| id | Yes | Numeric emoji id |

### Response

```json
{
  "status": "deleted",
  "hash": "a3b9...9c1f",
  "users_updated": 5
}
```

This endpoint is also available under `/v2/admin/emojis/:id` with the same behavior.
