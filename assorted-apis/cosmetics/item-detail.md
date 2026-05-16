# GET `/cosmetics/items/:id`

Get details for a single cosmetic by its ID.

**Authentication:** Not required.

**Path Parameter:**

| Parameter | Description |
|---|---|
| `:id` | The cosmetic's unique ID |

**Example:**

```http
GET /cosmetics/items/cat_ears
```

**Response (200):**

```json
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
```

**Error Responses:**

| Status | Error |
|---|---|
| `404` | `Cosmetic not found` |
