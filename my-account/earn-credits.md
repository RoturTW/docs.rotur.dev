# Earning Rotur Credits

Rotur Credits are the virtual currency used across the Rotur platform, powering purchases, apps, services, and premium features. As a developer, you can earn credits by creating **keys** that users can buy. Keys let you sell items, features, subscriptions, or anything else your service provides.

Users earn credits passively through daily rewards or client-specific systems, but the largest and most consistent source of credits in the economy comes from developers who create valuable things that users want to spend credits on.

## How Earning Credits Works

When a user purchases one of your keys:

1. Credits are transferred from the buyer to you.
2. A **10% fee** is automatically applied.
3. You receive **90% of the credits**.
4. If your key has a webhook, you receive a real-time event for fulfillment.

Keys can be created through the Key Manager:

**[https://rotur.dev/key-manager](https://rotur.dev/key-manager)**

Each key includes:

* A unique ID
* A price (in credits)
* A type (one-time or subscription)
* An optional webhook URL

You can also grant any of your keys to users for free via the UI or APIs.

## Key Types

### One-Time Keys

A simple single purchase. The user pays once and you receive credits immediately.

### Subscription Keys

These keys charge the user automatically on a repeating schedule. Supported cycles:

* Every **X days**
* Every **X weeks**
* Every **X months**
* Every **X years**

Subscription keys are ideal for:

* Premium app features
* Server plans
* Memberships
* Recurring unlocks

## Webhooks

If a key has a webhook configured, Rotur sends a POST request every time it is purchased. Recurring subscription charges also trigger a webhook.

### Webhook Payload

```json
{
  "username": "buyer_username",  // purchaser's Rotur username
  "key": "KEY_ID",               // the key that was purchased
  "price": 100,                  // price in credits
  "content": "buyer_username purchased key KEY_ID for 100 credits",
  "timestamp": 1732118400        // Unix timestamp
}
```

For recurring charges, `content` reads `buyer_username was charged by key: KEY_ID for 100 credits` instead.

This lets your backend:

* Activate purchases
* Deliver digital items
* Unlock app features
* Grant user roles or perks
* Track purchase events

## Developer Limits

On the free plan, you can create up to **5 keys**. Plus raises this to 20, and Pro to 50.

To create additional keys, subscribe to a higher [Ko-fi](https://ko-fi.com/mistium) tier.

You can view all your key data with:

```
GET https://api.rotur.dev/keys/mine
```

This returns every key you own, including prices, types, users, webhook configuration, and subscription settings.

## Refunds

Refunds are **not handled automatically** by the system.

If a user has been scammed or a mistake was made, they can contact:

* **@mistium** on Discord
* **@mist** on Rotur
* RMail or similar channels

Refunds and reversals can be processed manually when needed.

## How Users Earn Credits

### Daily Credits

Every user can claim free credits once every 24 hours via the `/claim_daily` endpoint. On OriginOS this is done through the **Wallet app**. The amount depends on your subscription tier (1 credit on Free, up to 3 on Pro).

### Client-Specific Systems

Some Rotur-connected clients (apps, OS environments, games, etc.) may offer their own earning mechanics.

## Economic Tracking

Rotur maintains full credit statistics internally, including total supply, circulation, transactions, developer earnings, and platform fees. You can see some of these via the public stats endpoints such as `https://api.rotur.dev/stats/economy`.

## Summary

* Developers earn credits by selling keys.
* Key sales carry a **10% fee**, with **90% going to the developer**.
* Keys can be one-time or subscription-based (days, weeks, months, or years).
* Webhooks notify you instantly when purchases and recurring charges happen.
* You can grant keys to users for free.
* Free tier developers can create up to 5 keys.
* Refunds are manual and handled by contacting support.
