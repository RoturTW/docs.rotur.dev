# Storage

`rotur.storage` stores key/value data for your app on the signed-in user's account. Every method needs a token.

Data is grouped by `id`: each id holds its own set of keys (for example, one id per app or project), separate from the user's account keys. Values persist across sessions and devices for that user.

## rotur.storage.get(id)

Gets every key and value stored under `id`.

**Auth:** Required. Sub-tokens need `storage:view`.

```ts
const { data } = await rotur.storage.get("my-app");
```

**Returns:** `{ id, data: Record<string, unknown> }`

## rotur.storage.getKey(id, key)

Gets one value stored under `id`. It calls `get(id)` and reads `key` from the result.

**Auth:** Required. Sub-tokens need `storage:view`.

```ts
const theme = await rotur.storage.getKey("my-app", "theme");
const volume = await rotur.storage.getKey<number>("my-app", "volume");
```

**Returns:** the value, or `undefined` when the key is not set.

## rotur.storage.set(id, key, value)

Sets one key under `id`.

**Auth:** Required. Sub-tokens need `storage:manage`.

```ts
const { data } = await rotur.storage.set("my-app", "theme", "dark");
```

**Returns:** `{ id, data }` after the change.

## rotur.storage.delete(id, key)

Deletes one key under `id`.

**Auth:** Required. Sub-tokens need `storage:delete`.

```ts
await rotur.storage.delete("my-app", "theme");
```

**Returns:** `{ id, data }` after the change.

## rotur.storage.clear(id)

Deletes every key under `id`.

**Auth:** Required. Sub-tokens need `storage:delete`.

```ts
const { cleared } = await rotur.storage.clear("my-app");
```

**Returns:** `{ id, cleared }`

## rotur.storage.clearAll()

Deletes all of the user's storage, under every id.

{% hint style="warning" %}
`clearAll()` wipes storage for every id on the account, not only your app's. Use `clear(id)` unless you mean all of it.
{% endhint %}

**Auth:** Required. Sub-tokens need `storage:delete`.

```ts
const { cleared } = await rotur.storage.clearAll();
```

**Returns:** `{ cleared }`

## rotur.storage.list()

Lists every storage id the user has, with total usage.

**Auth:** Required. Sub-tokens need `storage:view`.

```ts
const { ids, usage, max } = await rotur.storage.list();
```

**Returns:** `{ ids: string[], usage, max }`

## rotur.storage.usage()

Gets total storage usage and the quota.

**Auth:** Required. Sub-tokens need `storage:view`.

```ts
const { usage, max } = await rotur.storage.usage();
```

**Returns:** `{ usage, max }`
