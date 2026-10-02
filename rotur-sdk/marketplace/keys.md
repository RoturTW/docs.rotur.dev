# Keys

`rotur.keys` manages access keys: objects that users own, buy once, or subscribe to. `get()` and `check()` need no token; every other method does. Only the key's creator can rename, update, revoke, delete, or change the users of a key.

## rotur.keys.create(name, options?)

Creates a key.

**Auth:** Required. Sub-tokens need `keys:manage`.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | Yes | Key name |
| `options.description` | string | No | Description |
| `options.price` | number | No | Price in credits |
| `options.subscription` | boolean | No | Make the key a subscription |
| `options.frequency` | number | No | Billing frequency. Defaults to `1` |
| `options.period` | string | No | Billing period. Defaults to `"month"` |

```ts
const key = await rotur.keys.create("my-key", {
  description: "Premium feature access",
  price: 100,
  subscription: true,
  frequency: 1,
  period: "month",
});
```

**Returns:** `{ status, key, type, price, subscription? }`. `subscription` is `{ active, frequency, period, next_billing }`.

## rotur.keys.mine()

Lists the keys you created or bought. For keys you bought, `users` only holds your own entry, and `data`, `webhook`, and `total_income` are left out.

**Auth:** Required. Sub-tokens need `keys:view`.

```ts
const keys = await rotur.keys.mine();
```

**Returns:** `NetKey[]`: `{ key, name, price, type, users, creator, total_income?, webhook?, subscription?, data? }`. `users` maps each username to `{ time, price?, next_billing?, cancel_at? }`.

## rotur.keys.get(id)

Gets a key's public details.

**Auth:** None.

```ts
const key = await rotur.keys.get("key-id");
```

**Returns:** `{ key, name, price, type }`

## rotur.keys.check(username, key)

Checks whether a user owns a key.

**Auth:** None.

```ts
const { owned } = await rotur.keys.check("alice", "key-id");
```

**Returns:** `{ owned, username, key }`

## rotur.keys.rename(id, name)

Renames a key.

**Auth:** Required. Sub-tokens need `keys:manage`.

```ts
await rotur.keys.rename("key-id", "new-name");
```

**Returns:** `{ status }`

## rotur.keys.update(id, key, data)

Sets one field on a key. `key` is the field name and `data` is the new value. If `data` is valid JSON, it is stored parsed.

**Auth:** Required. Sub-tokens need `keys:manage`.

```ts
await rotur.keys.update("key-id", "data", "new-value");
```

**Returns:** `{ status }`

## rotur.keys.revoke(id, user)

Revokes a user's access to a key. You can't revoke your own access as the creator.

**Auth:** Required. Sub-tokens need `keys:manage`.

```ts
await rotur.keys.revoke("key-id", "bob");
```

**Returns:** `{ status }`

## rotur.keys.addUser(id, user)

Gives a user access to your key without them paying.

**Auth:** Required. Sub-tokens need `keys:manage`.

```ts
await rotur.keys.addUser("key-id", "alice");
```

**Returns:** `{ status }`

## rotur.keys.removeUser(id, user)

Removes a user from your key.

**Auth:** Required. Sub-tokens need `keys:manage`.

```ts
await rotur.keys.removeUser("key-id", "alice");
```

**Returns:** `{ status }`

## rotur.keys.buy(id)

Buys a key, or starts a subscription to it.

**Auth:** Required. Sub-tokens need `keys:manage`.

```ts
await rotur.keys.buy("key-id");
```

**Returns:** `{ message }`

## rotur.keys.cancel(id)

Cancels your subscription to a key. For a subscription key, you keep access until the next billing date, returned as `cancel_at`. For any other key, your access is removed straight away.

**Auth:** Required. Sub-tokens need `keys:manage`.

```ts
const { cancel_at } = await rotur.keys.cancel("key-id");
```

**Returns:** `{ status, cancel_at? }`

## rotur.keys.delete(id)

Deletes a key.

**Auth:** Required. Sub-tokens need `keys:manage`.

```ts
await rotur.keys.delete("key-id");
```

**Returns:** `{ status }`

## rotur.keys.debugSubscriptions()

Logs key subscription debug info on the server. Network admins only.

**Auth:** Required. Sub-tokens need `keys:view`.

```ts
const { message } = await rotur.keys.debugSubscriptions();
```

**Returns:** `{ message }`
