# POST `/cosmetics/purchase/:id`

Purchase or acquire a cosmetic. Free cosmetics are instantly added to your inventory. Paid cosmetics deduct credits from your balance and split revenue between the creator and the platform.

**Authentication:** Required. **Permission:** `cosmetics:buy`.

**Standing:** `warning` or above required.

{% hint style="info" %}
If you have any active subscription tier, the price is discounted by 20%. The discounted price goes entirely to the creator.
{% endhint %}

**Path Parameter:**

| Parameter | Description |
|---|---|
| `:id` | The cosmetic's unique ID |

**Example request:**

```http
POST /cosmetics/purchase/maga
```

**Example response (200), paid cosmetic:**

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

`price` is what you actually paid, and `discount` is how much you saved off the list price. `new_total` is your credit balance after the purchase.

**Example response (200), free cosmetic:**

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

For free cosmetics `new_total` is simply your current balance (nothing is deducted) and no `discount` field is included.

**Common errors:**

| Status | Error |
|---|---|
| `400` | `Invalid cosmetic id` |
| `400` | `You already own this cosmetic` |
| `400` | `You have reached the maximum number of owned cosmetics` |
| `400` | `Insufficient credits` (includes `required` and `available`) |
| `404` | `Cosmetic not found` |
| `500` | `Invalid creator percentage in catalog` |
