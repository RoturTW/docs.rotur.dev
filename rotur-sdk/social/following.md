# Following

Accessed via `rotur.following`. Follow/unfollow require auth; follower/following lists are public.

## Follow / Unfollow

```ts
await rotur.following.follow("alice");
await rotur.following.unfollow("alice");
```

## Get Followers

```ts
const { followers } = await rotur.following.followers("alice");
// ["bob", "charlie", ...]
```

## Get Following

```ts
const { following } = await rotur.following.following("alice");
// ["dave", "eve", ...]
```
