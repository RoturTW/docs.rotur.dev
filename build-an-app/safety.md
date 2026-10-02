---
description: Tell Rotur what your app does, and ask before messages and purchases so parental controls and teen defaults apply.
---

# Declarations and safety signals

Rotur looks after age checks, parental controls and teen defaults, so your app never needs to know anyone's age. For that to work in your app, you do two things:

1. **Declare** what your app does: whether people can talk to each other, whether it has mature content, and whether people can spend.
2. **Ask a signal** before someone sends a message or makes a purchase, and respect the answer.

## Declare what your app does

The owner sets declarations on the app's **Safety** page on [rotur.dev/me/developer](https://rotur.dev/me/developer). New apps start with nothing declared.

| Declaration | Turn it on if | What Rotur does |
| --- | --- | --- |
| **People can talk** (`communication`) | People can message or chat with each other in your app | Parents' message limits and teen defaults apply through the [message signal](#may-they-message). Parents see your app as one where people can talk |
| **Adults only** (`mature`) | Your app has content only adults should see | Under-18s, and anyone Rotur doesn't have a date of birth for, can't sign in. Their existing tokens stop working |
| **Spending** (`spending`) | People can spend credits or money in your app | Parents' spending limits apply through the [purchase signal](#may-they-spend) |

Keep them accurate, and update them before you add chat, mature content or payments ([Developer Terms](developer-terms.md), section 3).

Only an owner Rotur knows to be 18 or over can declare mature content. Content that's illegal, or that Rotur's Terms ban for everyone, isn't allowed in any app, mature or not.

Apps that used to be [systems](migrate-from-systems.md) start with nothing declared and a prompt to review.

### With the API

`PUT /v2/apps/<app>/declarations` with `{"communication": true, "mature": false, "spending": false}`. Only the owner can call it, signed in on rotur.dev; app credentials get `403`. Send all three: any you leave out are turned off. It returns your [app settings](apps-api.md#app-settings).

## How signals work

A signal is a yes-or-no answer with a sentence you can show the person:

```json
{ "allowed": false, "code": "settings", "reason": "Rotur settings don't allow this message." }
```

When the answer is yes, it's just `{"allowed": true}`.

Signals never say whether anyone is a child, or whose setting said no. A parent's limit, a teen default and an adult's own privacy setting all give the same answer. Show `reason` to the person and don't offer a way round it.

* **Auth:** [App credentials](apps-api.md) only.
* Both people must be [your users](users.md). Otherwise you get `404` with `Not one of your app's users: <name>`.
* Each signal needs its declaration. Without it you get `409` with `"code": "declaration_required"`.

## May they message?

`POST /v2/apps/<app>/signals/message`

Ask before someone sends a direct message, or anything else one person sends another.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `from` | body | string | Yes | The sender's username or Rotur user ID |
| `to` | body | string | Yes | The recipient's username or Rotur user ID |

Needs the **People can talk** declaration.

```sh
curl -X POST https://api.rotur.dev/v2/apps/$APP/signals/message \
  -u "$APP:$ROTUR_CLIENT_SECRET" -H "Content-Type: application/json" \
  -d '{"from": "kit", "to": "juniper"}'
```

**Response `200`:**

| `code` | `reason` | Why |
| --- | --- | --- |
| (none) | (none) | `allowed: true`. Send it |
| `banned` | You can't send messages in this app. | The sender can't use your app |
| `unavailable` | This person can't get messages here. | The recipient can't use your app |
| `blocked` | You can't message this person. | One has blocked the other on Rotur |
| `settings` | Rotur settings don't allow this message. | A parent's limit, a teen default or the recipient's own message settings |

## May they spend?

`POST /v2/apps/<app>/signals/purchase`

Ask before someone spends in your app. Rotur doesn't take the payment: it only says whether the purchase is allowed.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `user` | body | string | Yes | The buyer's username or Rotur user ID |
| `amount` | body | number | Yes | More than 0 |
| `currency` | body | string | Yes | `credits` for Rotur credits, or `money` for real money |
| `record` | body | boolean | No | For an allowed `credits` purchase, count it towards a parent's monthly limit. Send `true` when you're about to complete the purchase |
| `note` | body | string | No | What it was for. Rotur adds your app's name in front. Cut to 100 characters in total |

Needs the **Spending** declaration.

```sh
curl -X POST https://api.rotur.dev/v2/apps/$APP/signals/purchase \
  -u "$APP:$ROTUR_CLIENT_SECRET" -H "Content-Type: application/json" \
  -d '{"user": "kit", "amount": 50, "currency": "credits", "record": true, "note": "Gems"}'
```

**Response `200`:**

| `code` | `reason` | Why |
| --- | --- | --- |
| (none) | (none) | `allowed: true`. Go ahead |
| `unavailable` | This account can't use this app right now. | The buyer can't use your app |
| `settings` | Rotur settings don't allow this purchase. | It would go over a parent's credit limit, or it's real money and a parent has limited spending |

Real-money purchases are refused whenever a parent has set any spending limit.

**Errors:** `400` if `currency` isn't `credits` or `money` (`currency must be credits or money`), or `amount` isn't more than 0.
