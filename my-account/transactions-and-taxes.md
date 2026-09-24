# Transactions and Taxes

Rotur credits can be sent between users. This page covers sending credits, the fees on other credit operations, and the transaction history stored on your account.

> **Base URL:** `https://api.rotur.dev`
> **Auth:** `Authorization: Bearer <token>`. The legacy `auth` query parameter is also accepted.

## POST `/me/transfer`

Sends credits from your account to another user. `POST /v2/me/transfers` does the same.

**Auth:** Required. Sub-tokens need `credits:transfer`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `to` | body | string | Yes | Recipient username |
| `amount` | body | number or string | Yes | Credits to send, rounded to 2 decimal places. Minimum 0.01. Prefix a string with `£` to give the amount in GBP (see below). |
| `note` | body | string | No | Note stored on both transactions, up to 50 characters. Defaults to `transfer`. |

### Example

```http
POST /me/transfer
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "to": "mist", "amount": 5, "note": "Thanks for the help" }
```

**Response `200`:**

```json
{
  "message": "Transfer successful",
  "from": "your_username",
  "to": "mist",
  "amount": 5,
  "debited": 5
}
```

### Errors

All errors return `400` with an `error` message, except authentication errors, which return `403`.

| Error | When |
| --- | --- |
| `Invalid request payload` | The body is not valid JSON |
| `Amount must be provided` / `Invalid amount` | `amount` is missing or not a number |
| `Minimum amount is 0.01` | `amount` is below 0.01 after rounding |
| `Recipient username and amount must be provided` | `to` is missing |
| `Cannot send credits to yourself` | `to` is your own username |
| `recipient user not found` | No user has that username |
| `insufficient funds (required: X, available: Y)` | Your balance is too low |

### GBP amounts

Send `amount` as a string starting with `£`, such as `"£0.50"`, to give the amount in pounds. The server converts it to credits at the current platform exchange rate, which you can see at [`GET /stats/economy`](https://api.rotur.dev/stats/economy).

## Fees

Transfers between users are free. Some other operations charge a fee:

| Operation | Fee |
| --- | --- |
| Transfer between users | Free |
| Selling a key | 10%. The creator receives 90% of the price. |
| Selling a cosmetic | The creator receives the cosmetic's creator share; the rest goes to the platform. |
| Creating a credit gift | 1% on top of the gift amount |
| Creating a group | 15 credits |
| Unlocking banners | 30 credits, once. Free with Pro or higher. |
| Daily claim | Free. The owner of your account's system also receives 0.25 credits, recorded as `tax`. |

## Transaction history

Every credit operation is logged in `sys.transactions` on your account, newest first. Each entry has these fields:

| Field | Description |
| --- | --- |
| `type` | The kind of transaction (see below) |
| `user` | The username of the other party, if there is one |
| `amount` | Credits moved |
| `note` | Note, up to 50 characters |
| `time` | When it happened, in milliseconds |
| `new_total` | Your balance afterwards |
| `key_id`, `key_name` | The key involved, for key transactions |
| `provider`, `external_id` | The payment provider and its reference, for purchases |

How far back your history goes depends on your [subscription tier](subscriptions.md):

| Tier | History kept |
| --- | --- |
| Free | 1 month |
| Lite | 6 months |
| Plus | 12 months |
| Pro | Unlimited |

### Transaction types

| Type | Description |
| --- | --- |
| `in` | Credits received from another user, including daily claims |
| `out` | Credits sent to another user |
| `transfer_reversal` | A failed transfer refunded to you |
| `tax` | Your share of a daily claim made by a user on your system |
| `credit_purchase` | Credits bought through Stripe or Ko-fi |
| `key_buy` | Credits spent on a key, including recurring subscription charges |
| `key_sale` | Credits earned when someone buys your key (90% after the fee) |
| `item_buy` | Credits spent buying an item |
| `item_sale` | Credits earned selling an item |
| `gift_create` | Credits deducted to create a gift (amount plus 1%) |
| `gift_claim` | Credits received from claiming a gift |
| `gift_claimed` | Your gift was claimed by someone |
| `gift_refund` | Credits returned from a cancelled or expired gift |
| `escrow_out` | Credits sent to devfund escrow |
| `escrow_in` | Credits received from a devfund escrow release |
| `commerce_payment` | Credits paid through a commerce payment |
| `commerce_earning` | Credits received from a commerce payment |
| `bounty_funded` | Credits spent funding a bounty |
| `bounty_earning` | Credits received for winning a bounty |
| `bounty_refund` | Credits returned from a cancelled bounty |
| `group_create` | Credits spent creating a group |
| `group_entry_fee` | A group entry fee, paid or received |
| `group_tip` | Credits tipped to a group |
| `group_tip_withdrawal` | Credits withdrawn from a group's tips |
| `group_role_purchase` | Credits spent on a group role |
| `group_role_subscription` | A recurring charge for a group role |
| `cosmetic_purchase` | Credits spent on a cosmetic |
| `cosmetic_sale` | Credits earned from a cosmetic sale |
| `cosmetic_gift` | Credits spent gifting a cosmetic |
| `cosmetic_platform` | The platform's share of a cosmetic sale |
| `banner_unlock` | The one-time banner unlock fee |
| `pet_purchase` | Credits spent on a pet |
