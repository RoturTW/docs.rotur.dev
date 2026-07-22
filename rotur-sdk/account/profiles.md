# Profiles

Accessed via `rotur.profiles`. Most methods do **not** require authentication.

## Get a User Profile

```ts
const profile = await rotur.profiles.get("alice");
```

Returns a `UserProfile` with: `username`, `pfp`, `banner`, `bio`, `pronouns`, `system`, `created`, `followers`, `following`, `currency`, `subscription`, `max_size`, `badges`, `theme`, `private`, `banned`, `status`, `posts`, `followed`, `follows_me`, `id`, `index`.

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

## Image URL Helpers

Build avatar, overlay, and banner URLs. These are plain string helpers, so no request is made:

```ts
rotur.profiles.getAvatarUrl("alice");
// https://avatars.rotur.dev/alice?v=

rotur.profiles.getAvatarUrl("alice", "1234");
// https://avatars.rotur.dev/alice?v=1234

rotur.profiles.getOverlayUrl("alice");
// https://avatars.rotur.dev/.overlay/alice?v=

rotur.profiles.getBannerUrl("alice");
// https://avatars.rotur.dev/.banners/alice?v=
```

The second argument is an optional cache-busting value appended as `?v=`.
