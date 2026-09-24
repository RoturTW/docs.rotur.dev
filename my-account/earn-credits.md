# Earn credits

Rotur credits are the currency used across Rotur for purchases, apps and services. This page explains how developers earn credits by selling keys, and how users earn credits.

## Sell keys

A key is something users buy from you with credits: an app feature, a server plan, a membership, or anything else your service provides. The [Keys API](../assorted-apis/keys.md) has the full endpoint reference.

When a user buys one of your keys:

1. The price is taken from the buyer's balance.
2. Rotur keeps a 10% fee, and you receive 90% of the price.
3. If the key has a webhook, Rotur sends it a purchase event so you can deliver the purchase.

Create and manage keys in the [Key Manager](https://rotur.dev/key-manager) or through the API. Each key has:

* A unique ID
* A price in credits
* A type: one-time or subscription
* An optional webhook URL

You can also give a key to a user for free, from the Key Manager or the API.

### One-time keys

The user pays once and you receive the credits straight away.

### Subscription keys

The user is charged again at the end of each billing period. A period is a number of days, weeks, months or years, such as every 2 weeks. If the user can't pay a renewal, they lose access to the key.

### Key limits

The number of keys you can create depends on your [subscription tier](subscriptions.md):

| Tier | Keys |
| --- | --- |
| Free | 5 |
| Lite | 10 |
| Plus | 20 |
| Pro | 50 |

`GET /keys/mine` returns every key you own, with prices, types, users, webhook settings and subscription settings.

## Webhooks

If a key has a webhook, Rotur sends it a `POST` request with a JSON body each time the key is bought and each time a subscription renews.

| Field | Type | Description |
| --- | --- | --- |
| `username` | string | The buyer's Rotur username |
| `key` | string | The key ID |
| `price` | number | The price in credits |
| `content` | string | A readable summary of the event |
| `timestamp` | number | Unix timestamp, in seconds |

```json
{
  "username": "buyer_username",
  "key": "KEY_ID",
  "price": 100,
  "content": "buyer_username purchased key KEY_ID for 100 credits",
  "timestamp": 1732118400
}
```

For a subscription renewal, `content` reads `buyer_username was charged by key: KEY_ID for 100 credits`.

## Refunds

Refunds are not automatic. If a user was scammed or a purchase went wrong, they can contact **@mistium** on Discord, **@mist** on Rotur, or send an Rmail, and the refund is handled by hand.

## How users earn credits

* **Daily claim.** Every user can claim credits once every 24 hours with [`/claim_daily`](../claw/api-endpoints/claim_daily.md), or from the Wallet app on originOS. Free accounts get 1 credit and Pro accounts get 3; see [Daily credit claims](subscriptions.md#daily-credit-claims).
* **Transfers and sales.** Users receive credits from other users, and from selling items and cosmetics.
* **Apps and systems.** Some Rotur-connected apps, operating systems and games have their own ways to earn credits.

## Economy statistics

[`GET /stats/economy`](https://api.rotur.dev/stats/economy) returns public statistics about the credit economy, including the total supply and the current value of a credit in pence and cents.
