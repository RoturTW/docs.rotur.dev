# Items

`rotur.items` manages marketplace items: named objects with an owner, a transfer history, and an optional sale price. Reading items needs no token; changing them does.

Items are returned as `NetItem`: `{ name, description, price, selling, author, owner, private_data?, created, transfer_history, total_income }`. Each `transfer_history` entry is `{ from?, to, timestamp, type, price? }`.

## rotur.items.create(item)

Creates an item that you own.

**Auth:** Required. Sub-tokens need `items:manage`.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | Yes | Item name, used to look it up |
| `description` | string | No | Description |
| `price` | number | No | Price in credits |
| `selling` | boolean | No | List the item for sale |
| `data` | any | No | Private data, only visible to the owner |

```ts
const item = await rotur.items.create({
  name: "my-item",
  description: "A cool item",
  price: 50,
  selling: true,
  data: { color: "blue" },
});
```

**Returns:** `NetItem`

## rotur.items.get(name)

Gets an item.

**Auth:** None.

```ts
const item = await rotur.items.get("my-item");
```

**Returns:** `NetItem`

## rotur.items.list(username)

Lists the items a user owns.

**Auth:** None.

```ts
const items = await rotur.items.list("alice");
```

**Returns:** `NetItem[]`

## rotur.items.selling(limit?)

Lists items that are for sale. `limit` defaults to `50`.

**Auth:** None.

```ts
const items = await rotur.items.selling(50);
```

**Returns:** `NetItem[]`

## rotur.items.buy(name)

Buys an item that is for sale.

**Auth:** Required. Sub-tokens need `items:buy`.

```ts
await rotur.items.buy("my-item");
```

**Returns:** `{ message }`

## rotur.items.transfer(name, username)

Gives your item to another user.

**Auth:** Required. Sub-tokens need `items:manage`.

```ts
await rotur.items.transfer("my-item", "bob");
```

**Returns:** `{ message }`

## rotur.items.sell(name)

Lists your item for sale.

**Auth:** Required. Sub-tokens need `items:sell`.

```ts
await rotur.items.sell("my-item");
```

**Returns:** `{ message }`

## rotur.items.stopSelling(name)

Takes your item off sale.

**Auth:** Required. Sub-tokens need `items:sell`.

```ts
await rotur.items.stopSelling("my-item");
```

**Returns:** `{ message }`

## rotur.items.setPrice(name, price)

Sets your item's price in credits.

**Auth:** Required. Sub-tokens need `items:manage`.

```ts
await rotur.items.setPrice("my-item", 75);
```

**Returns:** `{ message }`

## rotur.items.update(name, data)

Updates your item's data. The object you pass is sent as `data`.

**Auth:** Required. Sub-tokens need `items:manage`.

```ts
const updated = await rotur.items.update("my-item", { color: "red" });
```

**Returns:** `NetItem`

## rotur.items.delete(name)

Deletes your item.

**Auth:** Required. Sub-tokens need `items:manage`.

```ts
await rotur.items.delete("my-item");
```

**Returns:** `{ message }`

## rotur.items.adminAdd(name, username)

Adds an item to a user (admin action).

**Auth:** Required. Sub-tokens need `items:manage`.

```ts
await rotur.items.adminAdd("my-item", "alice");
```

**Returns:** `NetItem`
