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

**Auth:** Required. No specific permission.

```ts
await rotur.me.update("bio", "Hello world");
await rotur.me.update("pronouns", "they/them");
```

`pfp` and `banner` accept data URIs and are uploaded for you. Setting `email` starts re-verification.

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
Deprecated. Use [`rotur.profiles.delete(username)`](profiles.md), which calls the same endpoint.
{% endhint %}

**Auth:** Required. Sub-tokens need `account:delete`.

**Returns:** `{ message }`

## rotur.me.changePassword(currentPassword, newPassword)

Changes your password.

**Auth:** Required. Main token only.

```ts
await rotur.me.changePassword("old-password", "new-password");
```

**Returns:** `{ message }`

## rotur.me.resendVerification()

Sends the email verification message again.

**Auth:** Required. No specific permission.

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

**Returns:** `{ message, from, to, amount, debited }`

## rotur.me.claimDaily()

Claims your daily credits.

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

**Auth:** Required. No specific permission.

```ts
const transactions = await rotur.me.transactions();
```

**Returns:** an array of `{ type, user, amount, note, time, new_total, petition_id?, key_name?, key_id? }`. Empty when there is no history.

## rotur.me.subscription()

Gets your subscription. It reads `sys.subscription` from `rotur.me.get()`.

**Auth:** Required. No specific permission.

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

**Returns:** `{ benefits, subscription }`. `benefits` has `max_keys`, `max_login_history`, `max_transaction_history`, `max_rmails`, `file_system_size`, `bio_length`, `animated_pfp`, `animated_banner`, `free_banner_uploads`, `bio_templating`, `profile_notes`, and `daily_credit_multiplier`.

## rotur.me.billing()

Gets your billing status.

**Auth:** Required. Sub-tokens need `account:view`.

```ts
const billing = await rotur.me.billing();
```

**Returns:** `{ provider, stripe_customer, stripe_portal, legacy_kofi, billing_configured, plus_trial_eligible, plus_trial_days, subscription, available_lookup_keys }`

## rotur.me.checkout(lookupKey)

Starts a checkout session for a subscription. `lookupKey` is one of the `available_lookup_keys` from `billing()`.

**Auth:** Required. Sub-tokens need `credits:manage`.

```ts
const { url } = await rotur.me.checkout(lookupKey);
window.location.href = url;
```

**Returns:** `{ url, session_id }`

## rotur.me.billingPortal()

Gets a link to the billing portal where you can manage your subscription.

**Auth:** Required. Sub-tokens need `account:view`.

```ts
const { url } = await rotur.me.billingPortal();
```

**Returns:** `{ url }`

## rotur.me.badges()

Gets the names of your badges.

**Auth:** Required. Sub-tokens need `account:view`.

```ts
const { badge_names } = await rotur.me.badges();
```

**Returns:** `{ badge_names: string[] }`

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

Sets your private note on a friend. Notes are a Plus-tier feature.

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
