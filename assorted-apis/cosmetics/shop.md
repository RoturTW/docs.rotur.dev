# GET `/cosmetics/shop`

Browse the cosmetics catalog, with filtering, search, sorting and pagination.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `type` | query | string | No | Only return this cosmetic type: `overlay` or `background` |
| `featured` | query | string | No | `true` returns only featured items |
| `search` | query | string | No | Case-insensitive match against name and description |
| `sort` | query | string | No | `newest` (default), `price_low`, `price_high` or `popular` (most purchases first) |
| `limit` | query | integer | No | Results per page. Default `50`, maximum `100` |
| `offset` | query | integer | No | Number of results to skip. Default `0` |

An invalid or non-positive `limit` falls back to `50`, and a `limit` above `100` is capped at `100`.

### Example

```http
GET /cosmetics/shop?type=overlay&sort=price_low&limit=10
```

**Response `200`:**

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

`total` is the number of items that match your filters, before pagination. `created_at` is a Unix timestamp in milliseconds.
