# Items

Accessed via `rotur.items`. Items are marketplace objects with ownership, transfer history, and optional selling.

## Create an Item

```ts
const item = await rotur.items.create({
  name: "my-item",
  description: "A cool item",
  price: 50,
  selling: true,
  data: { color: "blue" },  // private data (only visible to owner)
});
```

## Get an Item

```ts
const item = await rotur.items.get("my-item");
```

## List Items by User

```ts
const items = await rotur.items.list("alice");
```

## Browse Selling Items

```ts
const items = await rotur.items.selling(50);  // limit
```

## Buy an Item

```ts
await rotur.items.buy("my-item");
```

## Transfer an Item

```ts
await rotur.items.transfer("my-item", "bob");
```

## Sell / Stop Selling

```ts
await rotur.items.sell("my-item");          // list for sale
await rotur.items.stopSelling("my-item");   // delist
```

## Set Price

```ts
await rotur.items.setPrice("my-item", 75);
```

## Update Item Data

```ts
const updated = await rotur.items.update("my-item", {
  description: "Updated description",
  private_data: { color: "red" },
});
```

## Delete an Item

```ts
await rotur.items.delete("my-item");
```
