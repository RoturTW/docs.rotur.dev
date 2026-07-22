# Storage

Accessed via `rotur.storage`. App-scoped key/value storage on the signed-in user's account. All methods require authentication.

Each `id` namespaces an independent bag of keys (for example, one id per app or project), kept separate from the user's account object. Values persist across sessions and devices for that user.

## Read

```ts
// Every key/value pair stored under an id
const { data } = await rotur.storage.get("my-app");

// A single value (undefined if unset)
const theme = await rotur.storage.getKey("my-app", "theme");

// With a type parameter
const volume = await rotur.storage.getKey<number>("my-app", "volume");
```

## Write

```ts
// Set one key; returns the updated bag
const { data } = await rotur.storage.set("my-app", "theme", "dark");
```

## Delete

```ts
// Delete one key; returns the updated bag
await rotur.storage.delete("my-app", "theme");

// Remove every key under an id
await rotur.storage.clear("my-app");
// { id: "my-app", cleared: 5 }

// Remove ALL storage across every id
await rotur.storage.clearAll();
// { cleared: 12 }
```

{% hint style="warning" %}
`clearAll()` wipes storage for every id on the account, not just your app's. Use `clear(id)` unless you really mean all of it.
{% endhint %}

## Usage & Quota

```ts
// List every storage id, plus total usage
const { ids, usage, max } = await rotur.storage.list();

// Just usage and quota
const { usage, max } = await rotur.storage.usage();
```
