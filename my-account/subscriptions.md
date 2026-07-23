# Subscription Tiers

Rotur offers several subscription tiers, each building on the one below it. Tiers are hierarchical: every higher tier includes all the benefits of the lower tiers, plus new perks.

> **Note:** The **Drive** tier is an alias for **Pro**. Both receive identical benefits.

## Tier Hierarchy

```
Free → Lite → Plus → Pro
```

Each tier inherits everything from the tier below it and adds new benefits.

# Free

The default tier for all Rotur accounts. No subscription required.

> on rotur
- **5 Currency Keys**: create up to 5 keys for access control and API integrations
- **10 Login History Entries**: view up to 10 recent login records
- **20 Transaction History Entries**: view up to 20 recent credit transactions
- **100 Rmails**: store up to 100 rmail messages
- **5 MB File System**: 5 MB of file storage via the Rotur file system (OFSF)
- **200 Character Bio**: write a bio up to 200 characters
- **1x Daily Credit Multiplier**: earn 1 credit per daily claim

> on claw/pounce
- 400-character posts
- 1 pinned post
- 1 attachment per post

> on roturGate
- **20 free roturGate urls**: shorten 20 urls

# Lite - 15rc/month

A lightweight upgrade that introduces profile customization.

> on rotur
- **Everything in Free**, plus:
- **10 MB File System**: double the storage capacity (up from 5 MB)
- **Bio Templating**: use template expressions like `{{ user username }}` in your bio to dynamically display profile data

# Plus - £1/month

The sweet spot for active users who want animated avatars and more capacity.

> on rotur
- **Everything in Lite**, plus:
- **20 Currency Keys**: create up to 20 keys (up from 5)
- **100 Login History Entries**: view up to 100 recent login records (up from 10)
- **100 Transaction History Entries**: view up to 100 recent credit transactions (up from 20)
- **15 MB File System**: triple the base storage capacity (up from 10 MB)
- **500 Character Bio**: write a bio up to 500 characters (up from 200)
- **Animated Profile Picture**: upload and display an animated GIF as your avatar
- **2x Daily Credit Multiplier**: earn 2 credits per daily claim (up from 1x)
- **Friend Notes**: add private notes about other users via the `/me/note/:username` endpoint
- **`{{ time }}` Bio Template**: display your local time in your bio using `{{ time HH:MM }}`

> on claw/pounce
- 600-character posts
- Edit your posts
- 2 attachments per post
- 3 pinned posts
- Premium crown

> on mail
- **1,000 Rmails**: store up to 1,000 rmail messages (up from 100)

> on roturGate
- **100 roturGate urls**: have up to 100 shortened urls using rotur gate
- **Rename roturGate urls**: create custom https://gate.rotur.dev/:name urls for your redirects

> on sable
- Access to the full model catalogue
- 25% cheaper tokens compared to free

# Pro - £5/month

The premium tier for power users and developers.

> on rotur
- **Everything in Plus**, plus:
- **50 Currency Keys**: create up to 50 keys (up from 20)
- **1 GB File System**: 1 GB of file storage (up from 15 MB)
- **1,000 Character Bio**: write a bio up to 1,000 characters (up from 500)
- **Animated Banner**: upload and display an animated GIF as your profile banner
- **Free Banner Uploads**: banner uploads are free (normally 10 credits each)
- **500 Transaction History Entries**: view up to 500 recent credit transactions (up from 100)
- **3x Daily Credit Multiplier**: earn 3 credits per daily claim (up from 2x)
- **Pro Subscriber Badge**: a special badge displayed on your profile
- **`{{ url }}` Bio Template**: fetch and display external URL content in your bio using `{{ url https://... }}`. Can also be used to track profile visits.

> on claw/pounce
- 800-character posts
- 4 attachments per post
- 5 pinned posts

> on mail
- **100,000 Rmails**: store up to 100,000 rmail messages (up from 1,000)

> on roturGate
- **1000 roturGate urls**: have up to 1000 shortened urls using rotur gate

> on sable
- 50% cheaper tokens compared to free

{% hint style="info" %}
A special **Max** tier also exists (500 keys and 10 GB of storage on top of the Pro benefits). It is assigned by administrators and cannot be purchased.
{% endhint %}

### Daily Credit Claims

All users can claim daily credits via the `/claim_daily` endpoint, once every 24 hours. The amount depends on your tier:

| Tier | Daily Claim |
|---|---|
| Free | 1 credit |
| Lite | 1 credit |
| Plus | 2 credits |
| Pro  | 3 credits |

## Getting a Subscription

You can get a subscription through:

- **Ko-fi purchases**: subscribe on [Ko-fi](https://ko-fi.com/mistium). Your Rotur account is matched by your linked Discord account or your Ko-fi email, and the subscription runs for 31 days per billing cycle.
- **Admin assignment**: Rotur administrators can manually assign subscription tiers.
- **The Lite subscription key**: buying the Lite subscription key with credits grants the Lite tier for as long as the key subscription is active.

Subscriptions have a billing cycle and expire if not renewed. When a subscription expires, your account reverts to the **Free** tier and all benefits are reduced accordingly.
