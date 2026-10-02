# Profile cosmetics

Read the cosmetics other users have equipped, for example to draw their overlay on top of their avatar. You can look up one user or up to 100 at once.

## GET `/profile/:username/cosmetics`

Get one user's equipped cosmetics.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The user's username (case-insensitive) |

### Example

```http
GET /profile/mist/cosmetics
```

**Response `200`:**

```json
{
  "username": "mist",
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "active_cosmetics": {
    "overlay": {
      "id": "cat_ears",
      "cosmetic_type": "overlay",
      "name": "Cat Ears",
      "description": "Cute cat ear overlay for your avatar",
      "image_url": "",
      "pricing_type": "free",
      "price": 0,
      "creator": "mist",
      "creator_pct": 80,
      "featured": true,
      "purchases": 42,
      "created_at": 1715000000000,
      "raw_url": "https://api.rotur.dev/cosmetics/overlays/cat_ears.gif"
    }
  }
}
```

`active_cosmetics` is keyed by cosmetic type (`overlay`, `background`). Each entry has a `raw_url` you can load directly:

| Cosmetic | `raw_url` |
| --- | --- |
| Catalog overlay | `https://api.rotur.dev/cosmetics/overlays/<id>.gif` |
| Catalog background | `https://api.rotur.dev/cosmetics/backgrounds/<id>.mp4` |
| `custom_overlay` | `https://avatars.rotur.dev/.overlay/<username>` |
| `custom_background` | `https://avatars.rotur.dev/.backgrounds/<username>` |

If an equipped ID is no longer in the catalog, the entry only has `id`, `cosmetic_type`, `name` (set to the ID), `image_url` and `raw_url` filled in.

### Errors

| Status | When |
| --- | --- |
| `400` | `Username is required` |
| `403` | `User is banned` |
| `404` | `User not found` |
| `500` | `Failed to load cosmetics` |

## POST `/profile/cosmetics`

Get equipped cosmetics for up to 100 users in one request.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `usernames` | query | string | One of the two | Comma-separated usernames (`mist,allucat1000`) or a JSON array string. Can be repeated |
| `usernames` | body | string[] | One of the two | Usernames as a JSON array. Only read when the query parameter is absent |

Usernames are trimmed and de-duplicated case-insensitively before the 100-user limit is checked.

### Example

```http
POST /profile/cosmetics
Content-Type: application/json

{ "usernames": ["mist", "allucat1000"] }
```

The same request with the query parameter:

```http
POST /profile/cosmetics?usernames=mist,allucat1000
```

**Response `200`:**

```json
{
  "mist": {
    "username": "mist",
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "active_cosmetics": {
      "overlay": {
        "id": "cat_ears",
        "cosmetic_type": "overlay",
        "name": "Cat Ears",
        "raw_url": "https://api.rotur.dev/cosmetics/overlays/cat_ears.gif"
      }
    }
  }
}
```

The response is keyed by lowercased username, and each value has the same shape as the single-user response (the example above is shortened). Users who do not exist, are banned, or have nothing equipped are left out.

### Errors

| Status | When |
| --- | --- |
| `400` | `usernames parameter is required (provide via ?usernames=a,b or JSON body {"usernames":[...]})` |
| `400` | `too many usernames (max 100 per request)` |
