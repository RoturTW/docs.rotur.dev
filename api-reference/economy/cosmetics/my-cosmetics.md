# GET `/cosmetics/mine`

List the cosmetics you own and the ones you have equipped, with full details for each.

**Auth:** Required. Sub-tokens need `cosmetics:view`.

### Example

```http
GET /cosmetics/mine
Authorization: Bearer <token>
```

**Response `200`:**

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

`active_cosmetics` is keyed by cosmetic type (`overlay`, `background`) and only contains types you have equipped. If you have uploaded a custom overlay or background, it appears with the ID `custom_overlay` or `custom_background` (see [Custom cosmetics](README.md#custom-cosmetics)). Owned IDs that are no longer in the catalog are left out.

### Errors

| Status | When |
| --- | --- |
| `500` | `Failed to load cosmetics` |
