# POST `/cosmetics/purchase/:id`

Purchase or acquire a cosmetic. Free cosmetics are instantly added to your inventory. Paid cosmetics deduct credits from your balance and split revenue between the creator and the platform.

**Authentication:** Required.

**Standing:** `warning` or above required.

**Path Parameter:**

| Parameter | Description |
|---|---|
| `:id` | The cosmetic's unique ID |

**Example:**

```http
POST /cosmetics/purchase/maga?auth=YOUR_TOKEN
```

**Response (200) — Paid cosmetic:**

```json
{
  "message": "Cosmetic purchased successfully",
  "cosmetic": {
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
    "purchases": 6,
    "created_at": 1715100000000
  },
  "price": 50,
  "creator_share": 40,
  "platform_share": 10,
  "new_total": 450
}
```

**Response (200) — Free cosmetic:**

```json
{
  "message": "Cosmetic acquired successfully",
  "cosmetic": {
    "id": "cat_ears",
    "cosmetic_type": "overlay",
    "name": "Cat Ears",
    "description": "",
    "image_url": "",
    "pricing_type": "free",
    "price": 0,
    "creator": "mist",
    "creator_pct": 0,
    "featured": true,
    "purchases": 43,
    "created_at": 1715000000000
  },
  "price": 0,
  "new_total": 0
}
```

**Error Responses:**

| Status | Error |
|---|---|
| `400` | `You already own this cosmetic` |
| `400` | `Insufficient credits` (includes `required` and `available`) |
| `404` | `Cosmetic not found` |
| `500` | `Invalid creator percentage in catalog` |
