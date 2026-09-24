# Friends

`rotur.friends` manages your friend list and friend requests. Every method needs a token. To list pending requests, use [`rotur.me.requests()` and `rotur.me.outgoing()`](../account/me.md).

## rotur.friends.list()

Lists your friends.

**Auth:** Required. Sub-tokens need `friends:view`.

```ts
const { friends } = await rotur.friends.list();
// ["alice", "bob"]
```

**Returns:** `{ friends: string[] }`

## rotur.friends.request(username)

Sends a friend request. If the other user already sent you one, you become friends.

**Auth:** Required. Sub-tokens need `friends:request`.

```ts
await rotur.friends.request("alice");
```

**Returns:** `{ message }`

## rotur.friends.accept(username)

Accepts a friend request from `username`.

**Auth:** Required. Sub-tokens need `friends:accept`.

```ts
await rotur.friends.accept("bob");
```

**Returns:** `{ message }`

## rotur.friends.reject(username)

Rejects a friend request from `username`.

**Auth:** Required. Sub-tokens need `friends:accept`.

```ts
await rotur.friends.reject("charlie");
```

**Returns:** `{ message }`

## rotur.friends.cancel(username)

Cancels a friend request you sent that has not been accepted.

**Auth:** Required. Sub-tokens need `friends:cancel`.

```ts
await rotur.friends.cancel("alice");
```

**Returns:** `{ message }`

## rotur.friends.remove(username)

Removes a friend. The friendship is removed on both sides, along with any pending requests between you.

**Auth:** Required. Sub-tokens need `friends:remove`.

```ts
await rotur.friends.remove("bob");
```

**Returns:** `{ message }`
