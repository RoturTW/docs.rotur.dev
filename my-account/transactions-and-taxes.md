# Transactions and Taxes

Rotur has an integrated currency and economy system. You can send credits to other users and earn credits from them.

## Transferring Credits

Transfer credits with the `transfer` endpoint:

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
| `insufficient funds (required: X, available: Y)` | You don't have enough credits |

### GBP Amounts

You can specify amounts in GBP by prefixing with `£`. The system auto-converts using the current platform-wide exchange rate.

```json
{
  "to": "mist",
  "amount": "£0.50"
}
```

## Rotur Taxes

User-to-user transfers are completely free. There is no tax or fee on them.

Some other operations do have fees:

| Operation | Fee |
| --- | --- |
| Transfers between users | Free |
| Selling a key | 10% platform fee (you receive 90%) |
| Creating a gift | 1% tax on top of the gift amount |
| Banner upload | 10 credits (free with a Pro subscription) |
| Daily claim | Free for you; the owner of your system receives a small 0.25 credit tax payment |

## Transaction Types

Every credit operation is logged as a transaction on your account under `sys.transactions`. Each transaction records its type, amount, note, the other user, a timestamp, and your new total balance.

| Type | Description |
| --- | --- |
| `in` | Credits received from another user |
| `out` | Credits sent to another user |
| `tax` | Tax credit (e.g. the system owner's share of a daily claim) |
| `transfer` | Credits added from a Ko-fi credit purchase |
| `key_buy` | Credits spent to purchase a key |
| `key_sale` | Credits earned from selling a key (90% after the 10% fee) |
| `item_buy` | Credits spent buying an item |
| `item_sale` | Credits earned selling an item |
| `gift_create` | Credits deducted to create a gift (amount + 1% tax) |
| `gift_claim` | Credits received from claiming a gift |
| `gift_claimed` | Notification to the creator that their gift was claimed |
| `gift_refund` | Credits returned from a cancelled gift |
| `escrow_out` | Credits sent to devfund escrow |
| `escrow_in` | Credits received from a devfund escrow release |
| `group_create` | Credits spent creating a group |
| `group_entry_fee` | Credits paid or received as a group entry fee |
| `group_tip` | Credits sent as a tip to a group |
| `group_tip_withdrawal` | Credits withdrawn from a group's tips |
| `group_role_purchase` | Credits spent on a group role |
| `group_role_subscription` | Recurring charge for a group role |
| `cosmetic_purchase` | Credits spent on a cosmetic |
| `cosmetic_sale` | Credits earned from selling a cosmetic |
| `cosmetic_gift` | Credits spent gifting a cosmetic |
| `cosmetic_platform` | Platform share of a cosmetic sale |

The number of transactions kept depends on your subscription tier: 20 on Free and Lite, 100 on Plus, and 500 on Pro.
