# POST `/cosmetics/purchase/:id`

Buy a paid cosmetic or claim a free one. Free cosmetics are added to your inventory at no cost. Paid cosmetics are charged to your credit balance and the price is split between the creator and the platform.

**Auth:** Required. Sub-tokens need `cosmetics:buy`. Your account standing must be `warning` or better.

If you have an active subscription you pay 20% less, and the whole discounted price goes to the creator. See [Subscriber discount](README.md#subscriber-discount).

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The cosmetic ID |

### Example

```http
POST /cosmetics/purchase/maga
Authorization: Bearer <token>
```

**Response `200` (paid cosmetic):**

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
  "discount": 0,
  "creator_share": 40,
  "platform_share": 10,
  "new_total": 450
}
```

| Field | Description |
| --- | --- |
| `price` | What you paid, after any subscriber discount |
| `discount` | How much the subscriber discount saved you |
| `creator_share` / `platform_share` | How the price was split |
| `new_total` | Your credit balance after the purchase |

**Response `200` (free cosmetic):**

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
  "new_total": 500
}
```

For free cosmetics nothing is deducted, `new_total` is your current balance, and there are no `discount`, `creator_share` or `platform_share` fields.

### Errors

| Status | When |
| --- | --- |
| `400` | `Invalid cosmetic id` |
| `400` | `You already own this cosmetic` |
| `400` | `You have reached the maximum number of owned cosmetics` (200) |
| `400` | `Insufficient credits`. The body also has `required` and `available` |
| `403` | Your account standing is below `warning` |
| `404` | `Cosmetic not found` |
| `500` | `Invalid creator percentage in catalog` |
