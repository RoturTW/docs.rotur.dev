# Gifts

The gifts API lets you gift credits to other users through a redeemable code.

Sub-tokens need the matching `gifts:*` permission (`gifts:create`, `gifts:claim`, `gifts:cancel`, `gifts:view`).

**Base URL:** `https://api.rotur.dev`

## Create Gift

### POST `/gifts/create`

Creates a gift with a randomly generated redeem code. The amount plus a 1% tax is deducted from your balance. Requires authentication and `good` account standing.

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `amount` | number | Yes | Credits to gift, minimum 0.01 |
| `note` | string | No | An optional note, trimmed to 50 characters |
| `expires_in_hrs` | number | No | Hours until the gift expires, max 2160 (90 days). Omit for no expiry |

**Example:**

```bash
curl -X POST -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/gifts/create" \  -H "Content-Type: application/json" \
  -d '{"amount": 10, "note": "Thanks!", "expires_in_hrs": 168}'
```

**Response (200):**

```json
{
  "message": "Gift created successfully",
  "id": "gift_id",
  "code": "a1b2c3d4e5f6",
  "amount": 10,
  "tax": 0.1,
  "total_paid": 10.1,
  "expires_at": 1715659121000,
  "claim_url": "https://rotur.dev/gift/a1b2c3d4e5f6"
}
```

**Errors:** `400` for an invalid payload, an amount under 0.01, an expiry over 90 days, or insufficient funds (includes `required` and `available` fields).

## Get Gift

### GET `/gifts/:code`

Returns public information about a gift by its code. No authentication needed.

**Response (200):**

```json
{
  "gift": {
    "code": "a1b2c3d4e5f6",
    "amount": 10,
    "note": "Thanks!",
    "creator_id": "mist",
    "expires_at": 1715659121000
  }
}
```

**Errors:** `404` if the code does not exist. `410` if the gift was already claimed, cancelled, or has expired.

## Claim Gift

### POST `/gifts/claim/:code`

Claims a gift and adds the credits to your balance. Requires authentication and `warning` standing or better.

**Response (200):**

```json
{
  "message": "Gift claimed successfully",
  "amount": 10,
  "new_balance": 52.85
}
```

**Errors:** `400` if you try to claim your own gift, or if the gift was already claimed, cancelled, or expired. `404` if the code does not exist.

## Cancel Gift

### POST `/gifts/cancel/:id`

Cancels one of your unclaimed gifts and refunds the amount (the 1% tax is not refunded). Requires authentication.

**Path parameter:** the gift `id`, not the code.

**Response (200):**

```json
{
  "message": "Gift cancelled successfully",
  "refunded": 10,
  "new_balance": 52.85
}
```

**Errors:** `403` if the gift is not yours. `400` if it was already claimed, cancelled, or expired. `404` if the ID does not exist.

## My Gifts

### GET `/gifts/mine`

Returns all gifts you created, including claimed and cancelled ones. Requires authentication.

**Response (200):**

```json
{
  "gifts": [
    {
      "id": "gift_id",
      "code": "a1b2c3d4e5f6",
      "amount": 10,
      "note": "Thanks!",
      "creator_id": "mist",
      "created_at": 1715054321000,
      "expires_at": 1715659121000,
      "claimed_at": 1715054900000,
      "claimed_by": "rm"
    }
  ],
  "count": 1
}
```

`claimed_at` and `claimed_by` only appear on claimed gifts.
