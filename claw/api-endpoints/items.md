# Items

A marketplace for digital items. You can create items, list them for sale, buy other users' items, and transfer items you own.

> **Auth:** Looking up items (`/items/get`, `/items/list`, `/items/selling`) needs no token. Every other endpoint requires one; sub-tokens need the permission listed on each endpoint.

Item names are unique, ASCII only, and case-insensitive in paths. Prices are whole numbers of credits. All item endpoints are `GET` requests.

### Item object

```json
{
  "name": "Sword",
  "description": "",
  "price": 10,
  "selling": false,
  "author": "mist",
  "owner": "mist",
  "created": 1715054321,
  "transfer_history": [
    { "from": null, "to": "", "timestamp": 0, "type": "" }
  ],
  "total_income": 0
}
```

| Field | Description |
| --- | --- |
| `name` | Item name |
| `description` | Item description |
| `price` | Price in credits |
| `selling` | `true` if the item is listed for sale |
| `author` | Username of the creator |
| `owner` | Username of the current owner |
| `private_data` | Private data. `/items/get` returns it only to the owner, and `/items/list` and `/items/selling` never return it |
| `created` | Creation time, in Unix seconds |
| `transfer_history` | Ownership changes, each with `from`, `to`, `timestamp` (Unix seconds), `type` (`transfer` or `purchase`) and, for purchases, `price` |
| `total_income` | Total credits earned from sales of this item |

{% hint style="warning" %}
The first `transfer_history` entry (the item's creation) currently comes back empty, as shown above.
{% endhint %}

## GET `/items/create`

Creates an item owned by you.

**Auth:** Required. Sub-tokens need `items:manage`. Your account needs `good` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `item` | query | string | Yes | JSON object describing the item, with the fields below |

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | Yes | Unique item name, ASCII only |
| `description` | string | No | Item description |
| `price` | number | No | Price in credits, 0 or more. Decimals are cut off |
| `selling` | boolean | No | `true` lists the item for sale straight away |
| `data` | any | No | Private data only the owner can see |

### Example

```http
GET /items/create?item=%7B%22name%22%3A%22Sword%22%2C%22price%22%3A10%7D
Authorization: Bearer <token>
```

**Response `201`:** the new [item object](#item-object).

### Errors

| Status | When |
| --- | --- |
| `400` | `Item data is required` or `Invalid item data` |
| `400` | `Item name is required` |
| `400` | `Item name must contain only ASCII characters` |
| `400` | `Item with this name already exists` |
| `400` | `Price cannot be negative` |

## GET `/items/get/:name`

Returns an item by name. `private_data` is included only if you send the owner's token.

**Auth:** Optional.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | path | string | Yes | Item name |

### Example

```http
GET /items/get/sword
```

**Response `200`:** an [item object](#item-object).

### Errors

| Status | When |
| --- | --- |
| `404` | `Item not found` |

## GET `/items/list/:username`

Lists the items a user owns, without private data. An unknown username returns an empty list.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | Owner's username |

### Example

```http
GET /items/list/mist
```

**Response `200`:** an array of [item objects](#item-object).

## GET `/items/selling`

Lists items for sale with a price above 0, most recently created first, without private data.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `limit` | query | integer | No | Number of items to return, 1–100. Default 50 |

### Example

```http
GET /items/selling?limit=20
```

**Response `200`:** an array of [item objects](#item-object).

## GET `/items/buy/:name`

Buys an item that is for sale. The price moves from your balance to the seller, you become the owner, and the item is taken off sale. Both of you get a notification.

**Auth:** Required. Sub-tokens need `items:buy`. Your account needs at least `warning` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | path | string | Yes | Item name |

### Example

```http
GET /items/buy/sword
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Item 'Sword' purchased successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Item is not for sale` |
| `400` | `You cannot buy your own item` |
| `403` | `Insufficient currency` |
| `404` | `Item not found` |

## GET `/items/transfer/:name`

Gives an item you own to another user for free. They get an `item_received` notification.

**Auth:** Required. Sub-tokens need `items:manage`. Your account needs `good` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | path | string | Yes | Item name |
| `username` | query | string | Yes | Recipient's username. `to` also works |

### Example

```http
GET /items/transfer/sword?username=rm
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Item 'Sword' transferred successfully to rm"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Target username is required` |
| `400` | `You cannot transfer an item to yourself` |
| `403` | `You are not authorized to transfer this item` |
| `404` | `Target user not found` or `Item not found` |

## GET `/items/sell/:name`

Lists an item you own for sale at its current price. Set the price with `/items/set_price` first: items priced at 0 do not appear in `/items/selling`.

**Auth:** Required. Sub-tokens need `items:sell`. Your account needs `good` standing.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | path | string | Yes | Item name |

### Example

```http
GET /items/sell/sword
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Item is now for sale"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | `You are not authorized to sell this item` |
| `404` | `Item not found` |

## GET `/items/stop_selling/:name`

Takes an item you own off sale.

**Auth:** Required. Sub-tokens need `items:sell`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | path | string | Yes | Item name |

### Example

```http
GET /items/stop_selling/sword
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Item removed from sale"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | `You are not authorized to modify this item` |
| `404` | `Item not found` |

## GET `/items/set_price/:name`

Changes the price of an item you own.

**Auth:** Required. Sub-tokens need `items:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | path | string | Yes | Item name |
| `price` | query | integer | Yes | New price in credits, 0 or more |

### Example

```http
GET /items/set_price/sword?price=25
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Item price updated to 25"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Price is required` |
| `400` | `Invalid price` (not a whole number) |
| `400` | `Price cannot be negative` |
| `403` | `You are not authorized to modify this item` |
| `404` | `Item not found` |

## GET `/items/update/:name`

Changes the description or private data of an item you own.

**Auth:** Required. Sub-tokens need `items:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | path | string | Yes | Item name |
| `data` | query | string | Yes | JSON object with the fields to change: `description` (string) and/or `private_data` (any) |

### Example

```http
GET /items/update/sword?data=%7B%22description%22%3A%22Sharp%22%7D
Authorization: Bearer <token>
```

**Response `200`:** the updated [item object](#item-object).

### Errors

| Status | When |
| --- | --- |
| `400` | `New data is required` or `Invalid data` |
| `403` | `You are not authorized to update this item` |
| `404` | `Item not found` |

## GET `/items/delete/:name`

Deletes an item you own.

**Auth:** Required. Sub-tokens need `items:manage`.

{% hint style="warning" %}
Deletion is permanent and frees the name for anyone to use.
{% endhint %}

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | path | string | Yes | Item name |

### Example

```http
GET /items/delete/sword
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Item deleted successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | `You are not authorized to delete this item` |
| `404` | `Item not found` |

## GET `/items/admin_add/:id`

Sets an item's owner. Network admins only.

**Auth:** Required. Sub-tokens need `items:manage`. Network admin accounts only.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | Exact item name (case-sensitive) |
| `username` | query | string | Yes | New owner's username. `name` also works |

### Example

```http
GET /items/admin_add/Sword?username=rm
Authorization: Bearer <token>
```

**Response `200`:** the updated [item object](#item-object).

### Errors

| Status | When |
| --- | --- |
| `400` | `Username is required` |
| `403` | `Invalid authentication key` (you are not a network admin) |
| `404` | `Item not found` |
