# Subscription Tiers

Rotur offers several subscription tiers, each building on the benefits of the one below it. Tiers are hierarchical, every higher tier includes all the benefits of the lower tiers, plus additional perks.

> **Note:** The **Drive** tier is now an alias for **Pro**. Both receive identical benefits.

---

## Tier Hierarchy

```
Free → Lite → Plus → Pro
```

Each tier inherits all benefits from the tier below it and adds new ones.

---

## Quick Comparison

| Benefit | Free | Lite | Plus | Pro |
|---|---|---|---|---|
| **Max Currency Keys** | 5 | 5 | 20 | 50 |
| **Max Login History** | 10 | 10 | 100 | 100 |
| **Max Transaction History** | 20 | 20 | 100 | 500 |
| **Max Rmails** | 100 | 100 | 1,000 | 100,000 |
| **File System Size** | 5 MB | 10 MB | 15 MB | 1 GB |
| **Bio Length** | 200 chars | 200 chars | 500 chars | 1,000 chars |
| **Animated Profile Picture** | ❌ | ❌ | ✅ | ✅ |
| **Animated Banner** | ❌ | ❌ | ❌ | ✅ |
| **Free Banner Uploads** | ❌ | ❌ | ❌ | ✅ |
| **Bio Templating** | ❌ | ✅ | ✅ | ✅ |
| **Profile Notes** | ❌ | ❌ | ✅ | ✅ |
| **Daily Credit Multiplier** | 1× | 1× | 2× | 3× |
| **Pro Subscriber Badge** | ❌ | ❌ | ❌ | ✅ |
| **Bio `{{ time }}` Template** | ❌ | ❌ | ✅ | ✅ |
| **Bio `{{ url }}` Template** | ❌ | ❌ | ❌ | ✅ |

---

## Tier Details

### Free

The default tier for all Rotur accounts. No subscription required.

- **5 Currency Keys** - Create up to 5 keys for access control and API integrations
- **10 Login History Entries** - View up to 10 recent login records
- **20 Transaction History Entries** - View up to 20 recent credit transactions
- **100 Rmails** - Store up to 100 rmail messages
- **5 MB File System** - 5 MB of file storage via the Rotur file system (OFSF)
- **200 Character Bio** - Write a bio up to 200 characters
- **1× Daily Credit Multiplier** - Earn 1 credit per daily claim

---

### Lite

A lightweight upgrade that introduces profile customization.

- **Everything in Free**, plus:
- **10 MB File System** - Double the storage capacity (up from 5 MB)
- **Bio Templating** - Use template expressions like `{{ user username }}` in your bio to dynamically display profile data

---

### Plus

The sweet spot for active users who want animated avatars and more capacity.

- **Everything in Lite**, plus:
- **20 Currency Keys** - Create up to 20 keys (up from 5)
- **100 Login History Entries** - View up to 100 recent login records (up from 10)
- **100 Transaction History Entries** - View up to 100 recent credit transactions (up from 20)
- **1,000 Rmails** - Store up to 1,000 rmail messages (up from 100)
- **15 MB File System** - Triple the base storage capacity (up from 10 MB)
- **500 Character Bio** - Write a bio up to 500 characters (up from 200)
- **Animated Profile Picture** - Upload and display an animated GIF as your avatar
- **2× Daily Credit Multiplier** - Earn 2 credits per daily claim (up from 1×)
- **Profile Notes** - Add personal notes to other users' profiles via the `/me/note/:username` endpoint
- **`{{ time }}` Bio Template** - Display your local time in your bio using `{{ time HH:MM }}`

---

### Pro

The premium tier for power users and developers.

- **Everything in Plus**, plus:
- **50 Currency Keys** - Create up to 50 keys (up from 20)
- **100,000 Rmails** - Store up to 100,000 rmail messages (up from 1,000)
- **1 GB File System** - 1 GB of file storage (up from 15 MB)
- **1,000 Character Bio** - Write a bio up to 1,000 characters (up from 500)
- **Animated Banner** - Upload and display an animated GIF as your profile banner
- **Free Banner Uploads** - Banner uploads are free (normally cost 10 credits each)
- **500 Transaction History Entries** - View up to 500 recent credit transactions (up from 100)
- **3× Daily Credit Multiplier** - Earn 3 credits per daily claim (up from 2×)
- **Pro Subscriber Badge** - A special badge displayed on your profile
- **`{{ url }}` Bio Template** - Fetch and display external URL content in your bio using `{{ url https://... }}`. Can also be used to track number of profile visits.

---

### Daily Credit Claims

All users can claim daily credits via the `/claim_daily` endpoint. The amount received depends on your tier:

| Tier | Daily Claim |
|---|---|
| Free | 1 credit |
| Lite | 1 credit |
| Plus | 2 credits |
| Pro  | 3 credits |

---

## Getting a Subscription

Subscriptions can be obtained through:

- **Ko-fi purchases** - Buying a subscription via the Rotur Ko-fi shop automatically applies the tier to your account
- **Admin assignment** - Rotur administrators can manually assign subscription tiers
- **Key-based subscriptions** - Some access keys grant subscription access as a benefit

Subscriptions have a billing cycle and will expire if not renewed. When a subscription expires, it reverts to the **Free** tier and all benefits are reduced accordingly.
