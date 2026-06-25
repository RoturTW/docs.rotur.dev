# Keys

Accessed via `rotur.keys`. Keys are access-control objects that users can own, buy, and be granted.

## Create a Key

```ts
const key = await rotur.keys.create("my-key", {
  description: "Premium feature access",
  price: 100,
  subscription: true,
  frequency: 1,
  period: "month",
});
```

## List Your Keys

```ts
const keys = await rotur.keys.mine();
```

## Get a Key (Public)

```ts
const key = await rotur.keys.get("key-id");
```

## Check Key Ownership

```ts
const { owned } = await rotur.keys.check("alice", "key-id");
```

## Rename a Key

```ts
await rotur.keys.rename("key-id", "new-name");
```

## Update Key Data

```ts
await rotur.keys.update("key-id", "data", "new-value");
```

## Revoke Key Access

```ts
await rotur.keys.revoke("key-id", "bob");
```

## Admin Add / Remove User

```ts
await rotur.keys.addUser("key-id", "alice");
await rotur.keys.removeUser("key-id", "alice");
```

## Buy / Cancel Subscription

```ts
await rotur.keys.buy("key-id");
await rotur.keys.cancel("key-id");
```

## Delete a Key

```ts
await rotur.keys.delete("key-id");
```
