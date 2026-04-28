# Gifts

The gifts API allows users to create, claim, and cancel credit gifts that are redeemed via a code.

All endpoints requiring authentication use the `auth` query parameter.

**Base URL:** `https://social.rotur.dev`

## Create Gift

### POST `/gifts/create`

Creates a new gift with a randomly generated redeem code. The specified credit amount is deducted from your balance.

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `amount` | number | Yes | The credit amount to gift (minimum 1) |
| `note` | string | No | An optional note (max 100 chars) |
| `expires` | number | No | Expiry time in unix milliseconds (default 7 days) |

**Response (200):**

```json
{
  "id": "gift_abc123",
  "code": "a1b2c3d4e5f6",
  "amount": 10,
  "note": "Thanks for the help!",
  "expires_at": 1715659121000
}
```

Requires `good` account standing.

## Get Gift

### GET `/gifts/:code`

Returns public information about a gift by its code. Does not require authentication.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `code` | The gift code to look up |

**Response (200):**

```json
{
  "code": "a1b2c3d4e5f6",
  "amount": 10,
  "note": "Thanks for the help!",
  "creator_id": "mist",
  "expires_at": 1715659121000
}
```

## Claim Gift

### POST `/gifts/claim/:code`

Claims a gift by its code. The credit amount is added to your balance.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `code` | The gift code to claim |

**Response (200):**

```json
{
  "message": "gift claimed",
  "amount": 10,
  "new_total": 52.85
}
```

Requires `warning` standing or better.

## Cancel Gift

### POST `/gifts/cancel/:id`

Cancels an unclaimed gift. The credit amount is refunded to your balance.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `id` | The gift ID (not the code) |

**Response (200):**

```json
{
  "message": "gift cancelled",
  "amount": 10,
  "new_total": 52.85
}
```

## My Gifts

### GET `/gifts/mine`

Returns all gifts created by the authenticated user, including claimed and cancelled ones.

**Response (200):**

```json
{
  "gifts": [
    {
      "id": "gift_abc123",
      "code": "a1b2c3d4e5f6",
      "amount": 10,
      "note": "Thanks!",
      "creator_id": "mist",
      "created_at": 1715054321000,
      "expires_at": 1715659121000
    }
  ]
}
```

Expired gifts are automatically cancelled and refunded on an hourly basis.
