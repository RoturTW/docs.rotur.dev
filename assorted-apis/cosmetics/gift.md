# POST `/cosmetics/gift`

Gift a paid cosmetic to another user. You pay the list price plus a 1% gift tax, and the cosmetic goes straight into the recipient's inventory.

**Auth:** Required. Sub-tokens need `cosmetics:gift`. Your account standing must be `good`.

Free cosmetics cannot be gifted, since the recipient can claim them through the [purchase endpoint](purchase.md). The subscriber discount does not apply to gifts, and the price is split between creator and platform using the normal `creator_pct`. The tax is not paid to anyone.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `cosmetic_id` | body | string | Yes | ID of the cosmetic to gift |
| `to` | body | string | Yes | The recipient's username |
| `note` | body | string | No | A note for the recipient. Longer notes are cut to 50 characters |

### Example

```http
POST /cosmetics/gift
Authorization: Bearer <token>
Content-Type: application/json

{
  "cosmetic_id": "maga",
  "to": "allucat1000",
  "note": "Enjoy"
}
```

**Response `200`:**

```json
{
  "message": "Cosmetic gifted successfully",
  "gift_id": "a1b2c3d4...",
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
  "to": "allucat1000",
  "price": 50,
  "tax": 0.5,
  "total_paid": 50.5,
  "creator_share": 40,
  "platform_share": 10,
  "new_balance": 449.5
}
```

`new_balance` is your credit balance after the gift. The recipient gets a `cosmetic_gift` event containing `gift_id`, `cosmetic_id`, `cosmetic_name`, `from`, `note` and `amount`.

### Errors

| Status | When |
| --- | --- |
| `400` | `Invalid request payload` |
| `400` | `Invalid cosmetic id` |
| `400` | `Recipient username is required` |
| `400` | `You cannot gift a cosmetic to yourself` |
| `400` | `Free cosmetics cannot be gifted - the recipient can claim them directly` |
| `400` | `Insufficient credits`. The body also has `required` (price plus tax) and `available` |
| `400` | `Recipient already owns this cosmetic` |
| `400` | `Recipient has reached the maximum number of owned cosmetics` |
| `403` | Your account standing is below `good` |
| `404` | `Recipient user not found` |
| `404` | `Cosmetic not found` |
| `500` | `Invalid creator percentage in catalog` |
