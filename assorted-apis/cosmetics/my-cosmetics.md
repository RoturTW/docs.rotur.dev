# GET `/cosmetics/mine`

View your owned and active cosmetics. Returns full details for each cosmetic you own and your currently equipped cosmetics per type.

**Authentication:** Required (`auth` query parameter or `Authorization` header).

**Example:**

```http
GET /cosmetics/mine?auth=YOUR_TOKEN
```

**Response (200):**

```json
{
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
      "created_at": 1715000000000
    }
  },
  "owned_cosmetics": [
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
    },
    {
      "id": "maga",
      "cosmetic_type": "overlay",
      "name": "Maga",
      "description": "",
      "image_url": "",
      "pricing_type": "paid",
      "price": 50,
      "creator": "allucat1000",
      "creator_pct": 80,
      "featured": false,
      "purchases": 5,
      "created_at": 1715100000000
    }
  ]
}
```
