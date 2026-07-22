# Items

The items API is a marketplace for creating, buying, selling, and transferring digital items.

All endpoints except `get`, `list`, and `selling` require authentication. Sub-tokens need the matching `items:*` permission (`items:manage`, `items:buy`, `items:sell`).

**Base URL:** `https://api.rotur.dev`

## Create Item

### GET `/items/create`

Creates a new marketplace item. Requires `good` account standing.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| auth | Yes | Your authentication key |
| item | Yes | A JSON object describing the item (see below) |

The `item` JSON supports these fields:

| Field | Required | Description |
| --- | --- | --- |
| `name` | Yes | Unique item name, ASCII characters only |
| `description` | No | Item description |
| `price` | No | Price in credits, cannot be negative |
| `selling` | No | Whether the item is listed for sale right away |
| `data` | No | Private data only visible to the owner |

**Example:**

```bash
curl "https://api.rotur.dev/items/create?auth=YOUR_AUTH_KEY&item=%7B%22name%22%3A%22Sword%22%2C%22price%22%3A10%7D"
```

**Response (201):** the created item:

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
    { "to": "mist", "timestamp": 1715054321, "type": "creation" }
  ],
  "total_income": 0
}
```

## Get Item

### GET `/items/get/:name`

Returns an item by name. No authentication needed. If you authenticate as the owner, `private_data` is included.

## List User Items

### GET `/items/list/:username`

Returns all items owned by a user, without private data.

## Browse Selling Items

### GET `/items/selling`

Returns items currently listed for sale with a price above 0, newest first.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| limit | No | How many items to return. Default 50, max 100 |

## Buy Item

### GET `/items/buy/:name`

Purchases an item listed for sale. Credits move from you to the seller, and the item is delisted. Requires `warning` standing or better.

**Errors:** `400` if the item is not for sale or is your own, `403` for insufficient currency, `404` if not found.

## Transfer Item

### GET `/items/transfer/:name`

Transfers an item you own to another user at no cost. Requires `good` account standing.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| auth | Yes | Your authentication key |
| username | Yes | The recipient username. `to` also works |

**Errors:** `400` if you target yourself, `403` if you do not own the item, `404` if the item or user is not found.

## Sell Item

### GET `/items/sell/:name`

Lists an item you own for sale at its current price. Set the price first with `set_price`. Requires `good` account standing.

**Response:** `{ "message": "Item is now for sale" }`

## Stop Selling

### GET `/items/stop_selling/:name`

Removes your item from sale.

**Response:** `{ "message": "Item removed from sale" }`

## Set Price

### GET `/items/set_price/:name`

Updates the price of an item you own.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| auth | Yes | Your authentication key |
| price | Yes | New price in credits, cannot be negative |

## Update Item

### GET `/items/update/:name`

Updates the description or private data of an item you own.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| auth | Yes | Your authentication key |
| data | Yes | A JSON object with the fields to change: `description` and/or `private_data` |

**Response:** the updated item.

## Delete Item

### GET `/items/delete/:name`

Permanently deletes an item you own.

**Response:** `{ "message": "Item deleted successfully" }`

## Admin Add User

### GET `/items/admin_add/:id`

Reassigns an item's owner. Restricted to the platform admin; everyone else gets a `403`.
