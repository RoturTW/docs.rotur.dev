# Gifts

Send credits to someone through a redeem code. You create a gift, share its code or claim URL, and the first other user to claim it gets the credits.

> **Auth:** Looking up a gift by code needs no token. Every other endpoint requires one; sub-tokens need the permission listed on each endpoint.

Creating a gift costs its amount plus a 1% tax. Every gift expires, after at most 7 days. Gifts have a `kind` of `credits`, or `subscription` for paid membership gifts, which are bought through a separate checkout and cannot be created or cancelled with these endpoints.

## POST `/gifts/create`

Creates a credit gift with a random redeem code and takes the amount plus 1% tax from your balance.

**Auth:** Required. Sub-tokens need `gifts:create`. Your account needs `good` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `amount` | body | number | Yes | Credits to gift, at least 0.01. Rounded to 2 decimal places |
| `note` | body | string | No | Message for the recipient. Trimmed and cut to 50 characters |
| `expires_in_hrs` | body | integer | No | Hours until the gift expires, up to 168 (7 days). Default and `0` both mean 168 |

### Example

```http
POST /gifts/create
Authorization: Bearer <token>
Content-Type: application/json

{ "amount": 10, "note": "Thanks", "expires_in_hrs": 48 }
```

**Response `200`:**

```json
{
  "message": "Gift created successfully",
  "id": "gift_id",
  "code": "a1b2c3d4e5f6a7b8",
  "amount": 10,
  "tax": 0.1,
  "total_paid": 10.1,
  "expires_at": 1715227121000,
  "claim_url": "https://rotur.dev/gift?code=a1b2c3d4e5f6a7b8"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Invalid request payload` |
| `400` | `Minimum amount is 0.01` |
| `400` | `Maximum expiration is 7 days` (also returns `max_hours`) |
| `400` | `Insufficient funds` (also returns `required` and `available`) |

## GET `/gifts/:code`

Returns a gift by its code, whatever its state.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `code` | path | string | Yes | The gift's redeem code |

### Example

```http
GET /gifts/a1b2c3d4e5f6a7b8
```

**Response `200`:**

```json
{
  "gift": {
    "id": "gift_id",
    "code": "a1b2c3d4e5f6a7b8",
    "kind": "credits",
    "ready": true,
    "amount": 10,
    "note": "Thanks",
    "creator_id": "mist",
    "created_at": 1715054321000,
    "expires_at": 1715227121000,
    "is_expired": false
  }
}
```

`ready` is `true` while the gift can still be claimed. `creator_id` is the creator's username. `claimed_at` and `cancelled_at` appear once they apply, and `tier` appears on subscription gifts.

### Errors

| Status | When |
| --- | --- |
| `404` | `Gift not found` |

## POST `/gifts/claim/:code`

Claims a gift. For a credit gift, the amount is added to your balance.

**Auth:** Required. Sub-tokens need `gifts:claim`. Your account needs at least `warning` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `code` | path | string | Yes | The gift's redeem code |

### Example

```http
POST /gifts/claim/a1b2c3d4e5f6a7b8
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Gift claimed successfully",
  "gift_id": "gift_id",
  "kind": "credits",
  "amount": 10,
  "tier": "",
  "new_balance": 52.85
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `You cannot claim your own gift` |
| `400` | `This gift has already been claimed`, `This gift has been cancelled` or `This gift has expired` |
| `404` | `Gift not found` |
| `409` | A subscription gift, and you already have an active paid plan |

## POST `/gifts/cancel/:id`

Cancels one of your unclaimed credit gifts and refunds the amount. The 1% tax is not refunded.

**Auth:** Required. Sub-tokens need `gifts:cancel`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The gift's ID (not its code) |

### Example

```http
POST /gifts/cancel/gift_id
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Gift cancelled successfully",
  "refunded": 10,
  "new_balance": 52.85
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `This gift has already been claimed`, `This gift has already been cancelled` or `This gift has expired` |
| `400` | `Paid membership gifts cannot be cancelled here` |
| `403` | `You can only cancel your own gifts` |
| `404` | `Gift not found` |

## GET `/gifts/mine`

Lists every gift you have created, including claimed, cancelled and expired ones.

**Auth:** Required. Sub-tokens need `gifts:view`.

### Example

```http
GET /gifts/mine
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "gifts": [
    {
      "id": "gift_id",
      "code": "a1b2c3d4e5f6a7b8",
      "kind": "credits",
      "ready": false,
      "amount": 10,
      "note": "Thanks",
      "creator_id": "mist",
      "created_at": 1715054321000,
      "expires_at": 1715227121000,
      "claimed_at": 1715054900000,
      "claimed_by": "rm"
    }
  ],
  "count": 1
}
```

`claimed_at` and `claimed_by` (a username) only appear on claimed gifts.
