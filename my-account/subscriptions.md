# Subscription Tiers

Rotur has four subscription tiers:

```text
Free → Lite → Plus → Pro
```

Each tier includes all benefits from the tiers below it. When a higher tier lists a new limit for an existing benefit, that limit replaces the lower-tier limit.

> **Drive** is another name for the **Pro** tier. Drive and Pro accounts receive exactly the same benefits.

---

# Free

The default tier for every Rotur account. No subscription is required.

## Rotur

* **5 currency keys**
* **10 login history entries**
* **20 transaction history entries**
* **5 MB of file storage** through the Rotur file system
* **200-character bio**
* **1 credit per daily claim**

## Claw and Pounce

* Posts of up to **400 characters**
* **1 pinned post**
* **1 attachment per post**

## Mail

* Store up to **100 Rmails**

## RoturGate

* Create up to **20 shortened URLs**

## Connect

* **1 GB per day**
* **10 GB per month**

---

# Lite — 15 RC per month

A small upgrade for users who want more storage and additional profile customization.

Includes everything in **Free**, plus:

## Rotur

* **10 MB of file storage**, increased from 5 MB
* **Bio templates**, including expressions such as `{{ user username }}` for displaying dynamic profile information

---

# Plus — £1 per month

Designed for active users who want more capacity, profile customization, and additional features across Rotur services.

Includes everything in **Lite**, with the following upgrades:

## Rotur

* **20 currency keys**, increased from 5
* **100 login history entries**, increased from 10
* **100 transaction history entries**, increased from 20
* **15 MB of file storage**, increased from 10 MB
* **500-character bio**, increased from 200 characters
* **Animated profile pictures**
* **2 credits per daily claim**
* **Friend notes**, allowing you to privately save notes about other users through the `/me/note/:username` endpoint
* **Time bio templates** using expressions such as `{{ time HH:MM }}`

## Claw and Pounce

* Posts of up to **600 characters**
* Edit your own posts
* **2 attachments per post**
* **3 pinned posts**
* Premium crown

## Mail

* Store up to **1,000 Rmails**

## RoturGate

* Create up to **100 shortened URLs**
* Choose custom RoturGate paths, such as `https://gate.rotur.dev/:name`

## Sable

* Access to the full model catalogue
* Tokens cost **25% less** than on the Free tier

## Connect

* **10 GB per day**
* **100 GB per month**

---

# Pro — £5 per month

The highest tier, intended for power users and developers who need substantially higher limits.

Includes everything in **Plus**, with the following upgrades:

## Rotur

* **50 currency keys**, increased from 20
* **500 transaction history entries**, increased from 100
* **1 GB of file storage**, increased from 15 MB
* **1,000-character bio**, increased from 500 characters
* **Animated profile banners**
* **Free banner uploads**, instead of the usual 10-credit upload fee
* **3 credits per daily claim**
* Pro subscriber badge
* **URL bio templates** using expressions such as `{{ url https://... }}`, allowing your bio to display content retrieved from an external URL

> URL bio templates may also be used to track profile visits.

## Claw and Pounce

* Posts of up to **800 characters**
* **4 attachments per post**
* **5 pinned posts**

## Mail

* Store up to **100,000 Rmails**

## RoturGate

* Create up to **1,000 shortened URLs**

## Sable

* Tokens cost **50% less** than on the Free tier

## Connect

* **50 GB per day**
* **1 TB per month**

---

# Daily Credit Claims

Every user can claim credits once every 24 hours through the `/claim_daily` endpoint.

| Tier        | Credits per claim |
| ----------- | ----------------: |
| Free        |                 1 |
| Lite        |                 1 |
| Plus        |                 2 |
| Pro / Drive |                 3 |

---

# Getting a Subscription

Subscriptions can be obtained in three ways:

### Ko-fi

Subscribe through [Ko-fi](https://ko-fi.com/mistium).

Rotur attempts to match the purchase to your account using either:

* Your linked Discord account
* The email address associated with your Ko-fi purchase

Each Ko-fi billing cycle grants **31 days** of subscription access.

### Administrator assignment

A Rotur administrator can manually assign a subscription tier to an account.

### Lite subscription key

The Lite tier can be purchased using Rotur Credits. Your Lite subscription remains active for as long as the subscription key is active.

---

# Subscription Expiry

Subscriptions must be renewed at the end of each billing cycle. When a subscription expires, the account returns to the **Free** tier and its limits and available features are adjusted accordingly.
