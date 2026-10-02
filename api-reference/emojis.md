# Emojis

Custom emojis for Plus and Pro subscribers. You can upload emoji images, keep them on your account and add other users' emojis to your collection.

> **Base URL:** `https://api.rotur.dev`
>
> **Auth:** Send your token in the `Authorization: Bearer <token>` header (the legacy `auth` query parameter is also accepted). Any sub-token works; these endpoints need no specific permission. `GET /emojis/:emojiId` is public.

Every endpoint is also available under `/v2/emojis` with the same paths.

## Concepts

### IDs

Emojis are identified by a numeric ID. The image file is stored under the SHA-256 hash of its contents, and the ID is derived from that hash, so the same image always gets the same ID. Your uploaded and added emojis are listed separately on your account.

### Limits

The limit counts every emoji you have saved, uploaded and added together, with duplicates of the same image counted once.

| Tier | Saved emojis |
| --- | --- |
| Pro | 500 |
| Plus | 50 |
| Free and Lite | No access |

Without a Plus or higher subscription, every write endpoint returns `403` with `{ "error": "You have no subscription" }`, and `GET /emojis` returns `404`. An emoji whose uploader is no longer Plus or higher returns `404` from `GET /emojis/:emojiId` and cannot be added.

## GET `/emojis/:emojiId`

Returns the emoji image. Also accepts `HEAD`. Responses are cached for a day (`Cache-Control: public, max-age=86400`).

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `emojiId` | path | integer | Yes | The emoji ID |

### Example

```http
GET /emojis/12345
```

**Response `200`:** the image bytes, with a `Content-Type` of `image/png`, `image/gif` or `image/jpeg`.

### Errors

| Status | When |
| --- | --- |
| `404` | The ID is not a positive integer, no emoji has it, or its uploader is not Plus or higher. The body is empty. |

## GET `/emojis`

Lists your saved emojis.

**Auth:** Required.

### Example

```http
GET /emojis
Authorization: Bearer <token>
```

**Response `200`:**

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

`owner` is the uploader's user ID.

### Errors

| Status | When |
| --- | --- |
| `404` | Your account is not Plus or higher. The body is empty. |

## POST `/emojis`

Uploads an emoji and adds it to your uploaded list. Uploading an image you already have updates its name. If the image was in your added list, it moves to your uploaded list.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | body | string | Yes | Display name, up to 80 characters |
| `image` | body | string | Yes | A Base64 data URI of a PNG, GIF or JPEG image, up to 5 MB and 50 million pixels |

### Example

```http
POST /emojis
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "sparkles",
  "image": "data:image/png;base64,..."
}
```

**Response `200`:**

```json
{
  "status": "uploaded",
  "id": 12345,
  "hash": "a3b9...9c1f",
  "name": "sparkles",
  "content_type": "image/png",
  "url": "https://api.rotur.dev/emojis/12345"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The body is not valid JSON |
| `400` | `name` is missing or longer than 80 characters |
| `400` | `image` is not a valid Base64 data URI, is empty, is not PNG, GIF or JPEG, or is too large |
| `400` | You have reached your tier's emoji limit |
| `403` | You have no Plus or higher subscription |

## POST `/emojis/:emojiId/add`

Adds another user's uploaded emoji to your added list, optionally under a different name.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `emojiId` | path | integer | Yes | The emoji ID |
| `name` | body | string | No | A name to save it under, up to 80 characters. Defaults to the uploader's name. |

### Example

```http
POST /emojis/12345/add
Authorization: Bearer <token>
Content-Type: application/json

{
  "name": "favorite mist emoji"
}
```

**Response `200`:**

```json
{
  "status": "added",
  "id": 12345,
  "hash": "a3b9...9c1f",
  "name": "favorite mist emoji",
  "url": "https://api.rotur.dev/emojis/12345"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `emojiId` is not a positive integer |
| `400` | `name` is longer than 80 characters |
| `400` | You have reached your tier's emoji limit |
| `403` | You have no Plus or higher subscription |
| `404` | No emoji has this ID, or its uploader is not Plus or higher |

## POST `/emojis/:emojiId/unsave`

Removes an emoji from your added list. You cannot unsave an emoji you uploaded; delete it instead.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `emojiId` | path | integer | Yes | The emoji ID |

### Example

```http
POST /emojis/12345/unsave
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "status": "unsaved",
  "id": 12345
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `emojiId` is not a positive integer |
| `400` | The emoji is one you uploaded |
| `403` | You have no Plus or higher subscription |
| `404` | The emoji is not in your added list |

## DELETE `/emojis/:emojiId`

Deletes an emoji you uploaded.

**Auth:** Required. Only the uploader can delete an emoji.

{% hint style="warning" %}
Deleting an emoji also removes it from every user who added it, and deletes the image file. It cannot be undone.
{% endhint %}

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `emojiId` | path | integer | Yes | The emoji ID |

### Example

```http
DELETE /emojis/12345
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "status": "deleted",
  "id": 12345,
  "hash": "a3b9...9c1f",
  "users_updated": 1
}
```

`users_updated` is the number of accounts that had uploaded this image. The same image can be uploaded by more than one user, and deleting it removes it from all of them.

### Errors

| Status | When |
| --- | --- |
| `400` | `emojiId` is not a positive integer |
| `403` | You have no Plus or higher subscription |
| `403` | Someone else uploaded the emoji |
| `404` | No emoji has this ID |

## DELETE `/admin/emojis/:id`

Removes an emoji from every account and deletes its image. Also available at `DELETE /v2/admin/emojis/:id`.

**Auth:** Required. Network admins only.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | integer | Yes | The emoji ID |

### Example

```http
DELETE /admin/emojis/12345
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "status": "deleted",
  "id": 12345,
  "hash": "a3b9...9c1f",
  "users_updated": 5
}
```

`users_updated` is the number of accounts that had uploaded this image.

### Errors

| Status | When |
| --- | --- |
| `400` | `id` is not a positive integer |
| `404` | No emoji has this ID |
