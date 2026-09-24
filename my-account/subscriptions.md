# Subscription Tiers

A Rotur subscription raises your account limits and unlocks extra features across Rotur services. This page lists what each tier includes and how to get one.

There are four tiers. Each tier includes everything in the tiers below it.

| Tier | Price |
| --- | --- |
| Free | Free |
| Lite | 15 credits per month |
| Plus | £1 per month |
| Pro | £5 per month |

Plus and Pro can also be paid yearly, for the price of 8 months. **Drive** is an older name for Pro; Drive accounts get exactly the Pro benefits. The API also defines a **Max** tier above Pro (500 keys, 10 GB of storage, 50 MB notification log, 1,000-character posts), which is not sold through checkout.

To check your own tier and limits, call `GET /me/benefits`. Your tier is also in `sys.subscription` on your [account object](rotur-account-objects/README.md).

## Rotur account

| Benefit | Free | Lite | Plus | Pro |
| --- | --- | --- | --- | --- |
| [Keys](earn-credits.md) you can create | 5 | 10 | 20 | 50 |
| File storage | 5 MB | 25 MB | 100 MB | 1 GB |
| Bio length | 200 characters | 300 characters | 500 characters | 1,000 characters |
| [Credits per daily claim](#daily-credit-claims) | 1 | 1 | 2 | 3 |
| Login history entries | 10 | 25 | 100 | 100 |
| [Credit history](transactions-and-taxes.md#transaction-history) kept | 1 month | 6 months | 12 months | Unlimited |
| Notification log | 256 KB | 1 MB | 2 MB | 10 MB |
| [Bio templates](bio-templates.md) | — | Yes | Yes | Yes |
| [Friend notes](friend-notes.md) | — | — | Yes | Yes |
| Animated avatars and banners | — | — | Yes | Yes |
| Custom overlay uploads | — | — | Yes | Yes |
| Custom emojis for originChats and across Rotur | — | — | 50 | 500 |
| Banners without the 30-credit unlock | — | — | — | Yes |
| Custom profile backgrounds, including video | — | — | — | Yes |
| `pro` [badge](rotur-badges.md) | — | — | — | Yes |

## Claw and Pounce

| Benefit | Free | Lite | Plus | Pro |
| --- | --- | --- | --- | --- |
| Post length | 300 characters | 400 characters | 600 characters | 800 characters |
| Pinned posts | 1 | 1 | 3 | 5 |
| Attachments per post | 1 | 1 | 2 | 4 |
| Feed size | 100 posts | 100 posts | 200 posts | 200 posts |
| Edit your posts | — | — | Yes | Yes |
| Polls, scheduled posts and bookmarks | — | — | Yes | Yes |
| Premium crown on your posts | — | — | Yes | Yes |
| Video posts | — | — | — | Yes |

## Other services

| Benefit | Free | Lite | Plus | Pro |
| --- | --- | --- | --- | --- |
| Stored Rmails | 100 | 250 | 1,000 | 100,000 |
| RoturGate shortened URLs | 20 | 20 | 100 | 1,000 |
| RoturGate custom paths (`https://gate.rotur.dev/:name`) | — | — | Yes | Yes |
| Sable model catalogue | — | — | Full | Full |
| Sable token discount | — | — | 25% | 50% |
| Connect data per day | 1 GB | 1 GB | 10 GB | 50 GB |
| Connect data per month | 10 GB | 10 GB | 100 GB | 1 TB |

## Daily credit claims

Every user can claim credits once every 24 hours with [`/claim_daily`](../claw/api-endpoints/claim_daily.md). The amount depends on your tier:

| Tier | Credits per claim |
| --- | --- |
| Free | 1 |
| Lite | 1 |
| Plus | 2 |
| Pro | 3 |

## Get a subscription

| Method | Tiers | How it works |
| --- | --- | --- |
| Stripe | Plus, Pro | Subscribe monthly or yearly from your Rotur account (`POST /me/billing/checkout`). Accounts that have never subscribed get a 7-day trial of Plus. Manage or cancel the plan with `POST /me/billing/portal`. |
| Gift | Plus, Pro | Someone else buys a subscription for you as a gift. |
| Lite subscription key | Lite | Buy the Lite key with Rotur credits. Lite stays active for as long as the key does. |
| Ko-fi (legacy) | Any | Subscribe on [Ko-fi](https://ko-fi.com/mistium/tiers). Rotur matches the payment to your account by your linked Discord account, then by the email on the Ko-fi payment. Each payment gives 31 days. |
| Administrator | Any | A Rotur administrator can set your tier by hand. |

## Expiry

When a subscription reaches the end of its billing period without renewing, the account returns to the Free tier and Free limits apply again.
