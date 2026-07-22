# POST `/cosmetics/gift`

Gift a paid cosmetic to another user. You pay the full list price plus a 1% gift tax, and the cosmetic is added to the recipient's inventory immediately.

**Authentication:** Required. **Permission:** `cosmetics:gift`.

**Standing:** `good` required.

{% hint style="info" %}
Free cosmetics cannot be gifted. The recipient can claim those directly through the purchase endpoint. The subscriber discount does not apply to gifts.
{% endhint %}

**Request Body (JSON):**

| Field | Type | Required | Description |
|---|---|---|---|
| `cosmetic_id` | string | Yes | The ID of the cosmetic to gift |
| `to` | string | Yes | The recipient's username |
| `note` | string | No | A short note for the recipient (max 50 characters) |

**Example request:**

```http
POST /cosmetics/gift?auth=YOUR_TOKEN
Content-Type: application/json

{
  "cosmetic_id": "maga",
  "to": "allucat1000",
  "note": "Enjoy!"
}
```

**Example response (200):**

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

The recipient also receives a `cosmetic_gift` event with the gift details.

**Common errors:**

| Status | Error |
|---|---|
| `400` | `Invalid request payload` |
| `400` | `Invalid cosmetic id` |
| `400` | `Recipient username is required` |
| `400` | `You cannot gift a cosmetic to yourself` |
| `400` | `Free cosmetics cannot be gifted - the recipient can claim them directly` |
| `400` | `Insufficient credits` (includes `required` and `available`) |
| `400` | `Recipient already owns this cosmetic` |
| `400` | `Recipient has reached the maximum number of owned cosmetics` |
| `404` | `Recipient user not found` |
| `404` | `Cosmetic not found` |
