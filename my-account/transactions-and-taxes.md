# Transactions and Taxes

Across rotur is an integrated currency and economy system. This lets users transfer credits and earn credits from other users.

## Transferring Credits

Transferring credits is done via the `transfer` endpoint.

```
POST https://api.rotur.dev/me/transfer?auth=YOUR_AUTH_KEY
```

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `to` | string | Yes | Recipient username |
| `amount` | number or string | Yes | Amount to transfer (minimum 0.01). Prefix with `£` to specify GBP and auto-convert |
| `note` | string | No | Optional note for the transaction (max 50 chars) |

**Example:**
```json
{
  "to": "mist",
  "amount": 5,
  "note": "Thanks for the help!"
}
```

**Response (200):**
```json
{
  "message": "Transfer successful",
  "from": "your_username",
  "to": "mist",
  "amount": 5,
  "debited": 5
}
```

**Error Responses:**

| Error | Condition |
| --- | --- |
| `Minimum amount is 0.01` | Amount is below the minimum |
| `Cannot send credits to yourself` | Sender and recipient are the same |
| `sender user not found` | Your account could not be found |
| `recipient user not found` | The target username does not exist |
| `Insufficient funds` | You don't have enough credits |

### GBP Amounts

You can specify amounts in GBP by prefixing with `£`. The system will auto-convert based on the current platform-wide exchange rate.

```json
{
  "to": "mist",
  "amount": "£0.50"
}
```

## Rotur Taxes

Transfers are completely free and have no tax or fees.

## Transaction Types

Every credit operation is logged as a transaction on your account. Each transaction includes the type, amount, note, counterparty user, and your new total balance.

| Type | Description |
| --- | --- |
| `in` | Credits received from another user |
| `out` | Credits sent to another user |
| `tax` | Tax credit (e.g. daily claim system owner share) |
| `key_buy` | Credits spent to purchase a key |
| `key_sale` | Credits earned from selling a key (90% after 10% fee) |
| `gift_create` | Credits deducted to create a gift (amount + 1% tax) |
| `gift_claim` | Credits received from claiming a gift |
| `gift_claimed` | Notification to creator that their gift was claimed |
| `gift_refund` | Credits returned from a cancelled gift |
| `escrow_out` | Credits sent to devfund escrow |
| `escrow_in` | Credits received from devfund escrow release |
| `group_tip` | Credits sent as a tip to a group |

The number of transactions stored depends on your subscription tier (20 for Free, up to 500 for Pro).
