# Me

`rotur.me` reads and changes the signed-in account. Every method on this page needs a token.

For `checkAuth()`, `abilities()`, and `refreshToken()`, see [Authentication](authentication.md).

## rotur.me.get()

Gets the full account object.

**Auth:** Required. No specific permission.

```ts
const me = await rotur.me.get();
console.log(me.username, me.currency);
```

**Returns:** `MeData`: the profile fields (`username`, `pfp`, `bio`, `currency`, `subscription`, `badges`, …), every custom key on the account, and `sys.*` keys such as `sys.friends`, `sys.blocked`, `sys.transactions`, and `sys.subscription`.

With a sub-token you only get the parts of the account its permissions cover. Every token gets the public profile. `account:view` adds the custom keys and the profile `sys.*` keys, `credits:view` adds `sys.currency` and `sys.transactions`, `friends:view` adds `sys.friends` and `sys.requests`, `blocked:view` adds `sys.blocked`, `account:email` adds `email`, and `account:signins` adds the sign-in history. Under-18 accounts never release their email or sign-in history to apps. Other `sys.*` keys, such as billing, date of birth and sub account ownership, only come back to the main token.

## rotur.me.getKey(key)

Reads one account key from the WebSocket cache. No request is made.

**Auth:** Needs a connected WebSocket (see [Status and WebSocket](../realtime/status.md)).

```ts
const bio = rotur.me.getKey("bio");
```

**Returns:** `unknown`, or `undefined` when the key is not cached.

## rotur.me.getAllKeys()

Returns a copy of every cached account key. The cache is filled from the WebSocket `ready` message and kept current by `key_update` and `key_delete` messages.

**Auth:** Needs a connected WebSocket.

```ts
const keys = rotur.me.getAllKeys();
```

**Returns:** `Record<string, unknown>`

## rotur.me.onKeyChange(callback)

Calls `callback` whenever a cached account key changes or is deleted. On delete, `value` is `undefined`.

**Auth:** Needs a connected WebSocket.

```ts
const unsubscribe = rotur.me.onKeyChange((key, value, oldValue) => {
  console.log(key, "changed from", oldValue, "to", value);
});

unsubscribe();
```

**Returns:** a function that removes the listener. `rotur.socket.offKeyChange(callback)` also removes it.

## rotur.me.update(key, value)

Sets one key on your account.

**Auth:** Required. Sub-tokens need `account:profile`. The SDK's `METHOD_PERMISSIONS` does not list this yet, so add it to `requires` yourself.

```ts
await rotur.me.update("bio", "Hello world");
await rotur.me.update("pronouns", "they/them");
```

Keys can be up to 20 characters and values up to 1000. The whole account must stay under 25,000 bytes. You can't set `sys.*` keys, and `bio` is limited to your plan's `bio_length`.

`pfp` and `banner` must be data URIs and are uploaded for you. The first banner costs 30 credits unless your plan includes free banner uploads.

Changing `email` needs your password (or, for an account without one, a sign-in in the last 10 minutes on the main token). `update()` can't send a password, so send people to rotur.dev to change their email. A new email starts re-verification.

**Returns:** `{ message, username, key, value }`

## rotur.me.deleteKey(key)

Deletes one key from your account.

**Auth:** Required. Sub-tokens need `account:delete`.

```ts
await rotur.me.deleteKey("some_custom_key");
```

**Returns:** `{ message, username, key }`

## rotur.me.deleteAccount(username)

Deletes your account.

{% hint style="warning" %}
Deprecated. Use [`rotur.profiles.delete(username)`](profiles.md), which calls the same endpoint and has the same limits.
{% endhint %}

**Auth:** Required. Sub-tokens need `account:delete`.

**Returns:** `{ message, content_deleted }`

## rotur.me.changePassword(currentPassword, newPassword)

Changes your password.

**Auth:** Required. Main token only.

```ts
await rotur.me.changePassword("old-password", "new-password");
```

**Returns:** `{ message }`

## rotur.me.resendVerification()

Sends the email verification message again.

**Auth:** Required. Main token only.

```ts
await rotur.me.resendVerification();
```

**Returns:** `{ message }`

## rotur.me.acceptTos()

Accepts the terms of service for your account.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.me.acceptTos();
```

**Returns:** `{ message }`

## rotur.me.transfer(to, amount, note?)

Sends credits to another user.

**Auth:** Required. Sub-tokens need `credits:transfer`.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `to` | string | Yes | Username to send to |
| `amount` | number | Yes | Credits to send |
| `note` | string | No | Note shown on the transaction |

```ts
await rotur.me.transfer("bob", 50, "For the pizza");
```

The smallest transfer is 0.01 credits, and you can't send credits to yourself. Transfers between users are free. See [Transactions and taxes](../../api-reference/economy/transactions-and-taxes.md).

**Returns:** `{ message, from, to, amount, debited }`

## rotur.me.claimDaily()

Claims your daily credits. The account must be in good standing, and sub accounts can't claim.

**Auth:** Required. Sub-tokens need `credits:daily`.

```ts
await rotur.me.claimDaily();
```

**Returns:** `{ message }`

## rotur.me.claimTime()

Gets the time left until you can claim daily credits again.

**Auth:** Required. Sub-tokens need `credits:daily`.

```ts
const { wait_time } = await rotur.me.claimTime();
```

**Returns:** `{ wait_time: number }`

## rotur.me.transactions()

Gets your transaction history. It reads `sys.transactions` from `rotur.me.get()`.

**Auth:** Required. Sub-tokens need `credits:view`. Without it, `sys.transactions` is left out and you get an empty array.

```ts
const transactions = await rotur.me.transactions();
```

**Returns:** an array of `{ type, user, amount, note, time, new_total, petition_id?, key_name?, key_id? }`. Empty when there is no history.

## rotur.me.subscription()

Gets your subscription. It reads `sys.subscription` from `rotur.me.get()`.

**Auth:** Required. Sub-tokens need `account:view`. Without it, you get the `"Free"` fallback below.

```ts
const sub = await rotur.me.subscription();
// { active: true, tier: "Plus", next_billing: 1735689600000 }
```

**Returns:** `{ active, tier, next_billing }`. Without a subscription: `{ active: false, tier: "Free", next_billing: 0 }`.

## rotur.me.benefits()

Gets the limits and features your subscription tier gives you.

**Auth:** Required. Sub-tokens need `account:view`.

```ts
const { benefits, subscription } = await rotur.me.benefits();
console.log(benefits.max_keys, benefits.file_system_size);
```

**Returns:** `{ benefits, subscription, perk_restrictions }`.

- `benefits` has `max_keys`, `max_login_history`, `max_transaction_history` (deprecated), `transaction_history_months`, `max_rmails`, `notification_log_size`, `file_system_size`, `bio_length`, `animated_pfp`, `animated_banner`, `free_banner_uploads`, `custom_overlay_uploads`, `custom_background_uploads`, `external_url_bio_templates`, `bio_templating`, `profile_notes`, `daily_credit_multiplier`, and `max_sub_accounts`.
- `subscription` has `active`, `tier`, `next_billing`, `provider`, `external_id`, `status`, `cancel_at_period_end`, and `tenure_months`.
- `perk_restrictions` lists any cosmetic perks that have been turned off for your account.

See [Subscriptions](../../api-reference/account/subscriptions.md) for what each tier gives.

## rotur.me.billing()

Gets your billing status.

**Auth:** Required. Main token only.

```ts
const billing = await rotur.me.billing();
```

**Returns:** `{ cooling_off_ends, provider, stripe_customer, stripe_portal, legacy_kofi, billing_configured, plus_trial_eligible, plus_trial_days, subscription, available_lookup_keys }`. `available_lookup_keys` is `sable_credits_50`, `sable_credits_250`, `sable_credits_500`, `rotur_plus_monthly`, `rotur_pro_monthly`, `rotur_plus_yearly` and `rotur_pro_yearly`.

## rotur.me.checkout(lookupKey)

Starts a checkout session for a subscription or a Sable credit pack. `lookupKey` is one of the `available_lookup_keys` from `billing()`.

Rotur no longer sells Rotur credits. The old `rotur_credits_50`, `rotur_credits_250` and `rotur_credits_500` keys fail with `410` and code `product_retired`.

**Auth:** Required. Sub-tokens need `credits:manage`.

```ts
const { url } = await rotur.me.checkout(lookupKey);
window.location.href = url;
```

**Returns:** `{ url, session_id }`

## rotur.me.billingPortal()

Gets a link to the billing portal where you can manage your subscription. It fails with `404` until you have started a Plus or Pro checkout.

**Auth:** Required. Main token only.

```ts
const { url } = await rotur.me.billingPortal();
```

**Returns:** `{ url }`

## rotur.me.badges()

Gets your badges in the order they show on your profile.

**Auth:** Required. Sub-tokens need `account:view`.

```ts
const result = await rotur.me.badges();
// typed as { badge_names: string[] }; see below
```

**Returns:** `{ badges, badge_names, all_badges, preferences }`. `badges` holds the badge objects you show, in order. `badge_names` is a deprecated alias of `badges`: the SDK types it as `string[]`, but it holds the same badge objects. `all_badges` is every badge you have, with the same fields as `badges` in `badgePreferences()`, and `preferences` is your hidden list and order.

## rotur.me.badgePreferences()

Gets which of your badges are hidden and the order they show in.

**Auth:** Required. Sub-tokens need `account:view`.

```ts
const { preferences, badges } = await rotur.me.badgePreferences();
```

**Returns:** `{ preferences: { hidden_badges, badge_order }, badges }`. Each badge has `name`, `icon`, `description`, `hidden`, `pinned`, and optional `id`, `issuer`, `evolving`, `level`, `progress`, `next_threshold`.

## rotur.me.updateBadgePreferences(preferences)

Sets which badges are hidden and their order.

**Auth:** Required. Sub-tokens need `account:profile`.

```ts
await rotur.me.updateBadgePreferences({
  hidden_badges: ["early_adopter"],
  badge_order: ["subscriber"],
});
```

**Returns:** `{ preferences, badges }`, the same shape as `badgePreferences()`.

## rotur.me.blocked()

Lists the users you have blocked.

**Auth:** Required. Sub-tokens need `blocked:view`.

```ts
const { blocked } = await rotur.me.blocked();
```

**Returns:** `{ blocked: string[] }`

## rotur.me.block(username)

Blocks a user.

**Auth:** Required. Sub-tokens need `blocked:manage`.

```ts
await rotur.me.block("troll_user");
```

**Returns:** `{ message: "User blocked" }`

## rotur.me.unblock(username)

Unblocks a user.

**Auth:** Required. Sub-tokens need `blocked:manage`.

```ts
await rotur.me.unblock("troll_user");
```

**Returns:** `{ message: "User unblocked" }`

## rotur.me.notes()

Gets your private notes on other users.

**Auth:** Required. Sub-tokens need `account:profile`.

```ts
const { notes } = await rotur.me.notes();
// { alice: "Met at the meetup" }
```

**Returns:** `{ notes: Record<string, string> }`, keyed by username.

## rotur.me.note(username, content)

Sets your private note on a user. Notes need Plus or higher and can be up to 300 characters.

**Auth:** Required. Sub-tokens need `account:profile`.

```ts
await rotur.me.note("alice", "Met at the meetup");
```

**Returns:** `{ success: true }`

## rotur.me.deleteNote(username)

Deletes your note on a user.

**Auth:** Required. Sub-tokens need `account:profile`.

```ts
await rotur.me.deleteNote("alice");
```

**Returns:** `{ success: true }`

## rotur.me.requests()

Lists incoming friend requests.

**Auth:** Required. Sub-tokens need `friends:view`.

```ts
const { requests } = await rotur.me.requests();
// ["bob", "charlie"]
```

**Returns:** `{ requests: string[] }`

## rotur.me.outgoing()

Lists friend requests you have sent that are still pending.

**Auth:** Required. Sub-tokens need `friends:view`.

```ts
const { outgoing } = await rotur.me.outgoing();
```

**Returns:** `{ outgoing: string[] }`
