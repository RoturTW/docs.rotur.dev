# GET `/cosmetics/shop`

Browse the cosmetics shop. Returns a paginated, filterable list of available cosmetics.

**Authentication:** Not required.

**Query Parameters:**

| Parameter | Type | Default | Description |
|---|---|---|---|
| `type` | string | (none) | Filter by cosmetic type (e.g. `overlay`) |
| `featured` | string | (none) | Set to `true` to only return featured items |
| `search` | string | (none) | Search by name or description (case-insensitive) |
| `sort` | string | `newest` | Sort order: `newest`, `price_low`, `price_high`, `popular` |
| `limit` | int | `50` | Results per page (max 100) |
| `offset` | int | `0` | Pagination offset |

**Example request:**

```http
GET /cosmetics/shop?type=overlay&sort=price_low&limit=10
```

**Example response (200):**

```json
{
  "items": [
    {
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
      "created_at": 1715000000000
    }
  ],
  "total": 3,
  "offset": 0,
  "limit": 10
}
```
