# Profiles

Accessed via `rotur.profiles`. Most methods do **not** require authentication.

## Get a User Profile

```ts
const profile = await rotur.profiles.get("alice");
```

Returns a `UserProfile` with: `username`, `pfp`, `banner`, `bio`, `pronouns`, `system`, `created`, `followers`, `following`, `currency`, `subscription`, `badges`, `theme`, `private`, `banned`, `status`, `posts`, `followed`, `follows_me`, `id`.

```ts
// Without posts (faster)
const profile = await rotur.profiles.get("alice", false);
```

## Check if User Exists

```ts
const { exists } = await rotur.profiles.exists("alice");
```

## Get Supporters

Returns all non-free subscribers:

```ts
const supporters = await rotur.profiles.supporters();
// [{ username: "alice", subscription: "Plus" }, ...]
```

## Avatar URL

Helper to build an avatar URL:

```ts
const url = rotur.profiles.getAvatarUrl("alice");
// https://avatars.rotur.dev/alice?v=

const url = rotur.profiles.getAvatarUrl("alice", "1234");
// https://avatars.rotur.dev/alice?v=1234
```
