# GET `/profile/:username/cosmetics`

View another user's active cosmetics. Use this to render their overlay on top of their avatar.

**Authentication:** Not required.

**Path Parameter:**

| Parameter | Description |
|---|---|
| `:username` | The user's username (case-insensitive) |

**Example request:**

```http
GET /profile/mist/cosmetics
```

**Example response (200):**

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

Each active cosmetic includes a `raw_url` pointing at the raw asset, ready to load into an `<img>` tag.

**Common errors:**

| Status | Error |
|---|---|
| `400` | `Username is required` |
| `403` | `User is banned` |
| `404` | `User not found` |

## Batch lookup

Look up active cosmetics for up to **100** users in one request. Available as `GET` or `POST` on `/profile/cosmetics` (v2: `POST /v2/profiles/cosmetics`).

Provide usernames either as a query parameter:

```http
GET /profile/cosmetics?usernames=mist,allucat1000
```

Or as a JSON body on POST:

```http
POST /profile/cosmetics
Content-Type: application/json

{ "usernames": ["mist", "allucat1000"] }
```

**Example response (200):**

```json
{
  "mist": {
    "username": "mist",
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "active_cosmetics": {
      "overlay": { "id": "cat_ears", "raw_url": "https://api.rotur.dev/cosmetics/overlays/cat_ears.gif" }
    }
  }
}
```

Keys are lowercased usernames. Users that do not exist, are banned, or have no active cosmetics are silently left out of the response.

**Common errors:**

| Status | Error |
|---|---|
| `400` | `usernames parameter is required` |
| `400` | `too many usernames (max 100 per request)` |
