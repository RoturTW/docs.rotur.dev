# Friends

Accessed via `rotur.friends`. All methods require authentication.

## List Friends

```ts
const { friends } = await rotur.friends.list();
// ["alice", "bob", ...]
```

## Send a Friend Request

```ts
await rotur.friends.request("alice");
```

If the target already considers you a friend, this auto-accepts both ways.

## Accept a Friend Request

```ts
await rotur.friends.accept("bob");
```

## Reject a Friend Request

```ts
await rotur.friends.reject("charlie");
```

## Remove a Friend

```ts
await rotur.friends.remove("bob");
```

This cleans up the friendship on both sides and removes any pending requests.
