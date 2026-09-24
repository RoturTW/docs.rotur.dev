# Following

`rotur.following` follows users and reads follower lists. Following and unfollowing need a token; the lists are public.

## rotur.following.follow(username)

Follows a user.

**Auth:** Required. Sub-tokens need `following:follow`.

```ts
await rotur.following.follow("alice");
```

**Returns:** `{ message }`

## rotur.following.unfollow(username)

Unfollows a user.

**Auth:** Required. Sub-tokens need `following:unfollow`.

```ts
await rotur.following.unfollow("alice");
```

**Returns:** `{ message }`

## rotur.following.followers(username)

Lists the users who follow `username`.

**Auth:** None.

```ts
const { followers } = await rotur.following.followers("alice");
// ["bob", "charlie"]
```

**Returns:** `{ followers: string[] }`

## rotur.following.following(username)

Lists the users `username` follows.

**Auth:** None.

```ts
const { following } = await rotur.following.following("alice");
// ["dave", "eve"]
```

**Returns:** `{ following: string[] }`
