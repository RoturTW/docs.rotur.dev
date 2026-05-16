# Items

The items API provides a marketplace for creating, buying, selling, and transferring digital items. All endpoints requiring authentication use the `auth` query parameter.

**Base URL:** `https://api.rotur.dev`

## Create Item

### GET `/items/create`

Creates a new marketplace item.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| auth | Yes | Your Rotur authentication key |
| name | Yes | Unique item name |
| description | Yes | Item description |
| price | Yes | Price in credits |
| private\_data | No | Private data only visible to the owner |

Requires `good` account standing.

## Get Item

### GET `/items/get/:name`

Returns public information about an item.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `name` | The item name |

## List User Items

### GET `/items/list/:username`

Returns all items owned by a user.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `username` | The Rotur username |

## Browse Selling Items

### GET `/items/selling`

Returns all items currently listed for sale.

## Buy Item

### GET `/items/buy/:name`

Purchases an item listed for sale. Credits are transferred from buyer to seller.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `name` | The item name to buy |

Requires `warning` standing or better.

## Transfer Item

### GET `/items/transfer/:name`

Transfers an item you own to another user at no cost.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| auth | Yes | Your Rotur authentication key |
| username | Yes | The recipient username |

Requires `good` account standing.

## Sell Item

### GET `/items/sell/:name`

Lists an item you own for sale at a specified price.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| auth | Yes | Your Rotur authentication key |
| price | Yes | Sale price in credits |

Requires `good` account standing.

## Stop Selling

### GET `/items/stop_selling/:name`

Removes an item from the marketplace listing.

## Set Price

### GET `/items/set_price/:name`

Updates the sale price of an item currently listed for sale.

**Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| auth | Yes | Your Rotur authentication key |
| price | Yes | New sale price |

## Update Item

### GET `/items/update/:name`

Updates item metadata (description, private data, etc.).

## Delete Item

### GET `/items/delete/:name`

Permanently deletes an item you own.

## Admin Add User

### GET `/items/admin_add/:id`

As the item creator, manually grants access to a user for an item you created.
