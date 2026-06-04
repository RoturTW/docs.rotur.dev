# Me — Your Account

Accessed via `rotur.me`. All methods require authentication.

## Get Your Data

```ts
const me = await rotur.me.get();
// Returns the full user object (all keys except password)
```

## Update Profile Key

```ts
await rotur.me.update("bio", "Hello world");
await rotur.me.update("pronouns", "they/them");
```

Special keys: `pfp` and `banner` accept data URIs and handle upload automatically. Setting `email` triggers re-verification.

## Delete a Key

```ts
await rotur.me.deleteKey("some_custom_key");
```

## Delete Account

```ts
await rotur.me.deleteAccount();
```

## Credits & Transfers

```ts
// Transfer credits to another user
await rotur.me.transfer("bob", 50, "For the pizza");

// Claim daily credits
await rotur.me.claimDaily();

// Check time until next claim
const { wait_time } = await rotur.me.claimTime();
```

## Badges

```ts
const { badge_names } = await rotur.me.badges();
// e.g. ["early_adopter", "subscriber"]
```

## Blocking

```ts
// List blocked users
const { blocked } = await rotur.me.blocked();

// Block/unblock
await rotur.me.block("troll_user");
await rotur.me.unblock("troll_user");
```

## Friend Notes

Notes are a Plus-tier feature that let you add a private note to a friend:

```ts
await rotur.me.note("alice", "Met at the meetup");
await rotur.me.deleteNote("alice");
```

## Pending Friend Requests

```ts
const { requests } = await rotur.me.requests();
// e.g. ["bob", "charlie"]
```

## Transactions

```ts
const txs = await rotur.me.transactions();
// Array of { type, user, amount, note, time, new_total, ... }
```

## Subscription

```ts
const sub = await rotur.me.subscription();
// { active: true, tier: "Plus", next_billing: 1735689600000 }
```

## Email Verification

```ts
// Resend verification email
await rotur.me.resendVerification();

// Verify with token from email link
await rotur.me.verifyEmail("token-from-email");
```

## Terms of Service

```ts
await rotur.me.acceptTos();
```
