# Products & Subscriptions

Groups can sell **role products**: pay credits, get a role (and its benefits). Products can be one-time purchases or recurring subscriptions billed automatically.

## List Products

### GET `/v2/groups/{tag}/products`

**Auth:** required. Token permission: `groups:view`.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/products?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
[
  {
    "id": "prod-1",
    "group_tag": "mygroup",
    "name": "VIP",
    "description": "VIP role with perks",
    "price_credits": 100.0,
    "role_granted_id": "role-3",
    "role_name": "VIP",
    "subscription": true,
    "frequency": 1,
    "period": "month"
  }
]
```

`role_granted_id`, `role_name`, `benefit_granted`, `frequency`, and `period` are omitted when empty.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |

***

## Create a Product

### POST `/v2/groups/{tag}/products`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.roles.manage` group permission.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | Yes | Product name (max 50 chars) |
| `description` | string | No | Product description (max 200 chars) |
| `price_credits` | float | Yes | Price in credits (must be positive) |
| `role_id` | string | Yes | Role granted on purchase (not the Owner role) |
| `subscription` | string | No | `"true"` for a recurring subscription (default: `"false"`) |
| `frequency` | int | No | Billing every N periods (default: 1, subscriptions only) |
| `period` | string | No | `"day"`, `"week"`, `"month"`, or `"year"` (default: `"month"`, subscriptions only) |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/products?auth=YOUR_TOKEN&name=VIP&price_credits=100&role_id=role-3&subscription=true&period=month"
```

**Example response (201):** the created product object, as in the list response.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Name is required` | No name provided |
| 400 | `Invalid price` | Price missing or not positive |
| 400 | `Invalid frequency` / `Invalid period` | Bad subscription settings |
| 400 | `Owner role cannot be sold` | `role_id` is the Owner role |
| 403 | `You don't have permission to manage role products` | Missing `groups.roles.manage` |
| 404 | `Role not found` | `role_id` doesn't exist in this group |
| 404 | `Group not found` | Group doesn't exist |

***

## Delete a Product

### DELETE `/v2/groups/{tag}/products/{productid}`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.roles.manage` group permission.

**Example request:**

```bash
curl -X DELETE "https://api.rotur.dev/v2/groups/mygroup/products/prod-1?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Product deleted"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to manage role products` | Missing `groups.roles.manage` |
| 404 | `Product not found` | Product ID doesn't exist |
| 404 | `Group not found` | Group doesn't exist |

***

## Purchase a Product

### POST `/v2/groups/{tag}/products/{productid}/purchase`

**Auth:** required. Token permission: `credits:manage`. You must be a member of the group.

Charges the price to your account (a `group_role_purchase` transaction), grants you the role, and adds the credits to the group's balance. For subscription products, an active subscription is created and billed automatically each period. If a renewal fails (not enough credits), the subscription is deactivated and the role is removed.

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/products/prod-1/purchase?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Product purchased",
  "product": {
    "id": "prod-1",
    "group_tag": "mygroup",
    "name": "VIP",
    "price_credits": 100.0,
    "role_granted_id": "role-3",
    "role_name": "VIP",
    "subscription": true,
    "frequency": 1,
    "period": "month"
  },
  "subscription": {
    "id": "sub-1",
    "group_tag": "mygroup",
    "product_id": "prod-1",
    "product_name": "VIP",
    "username": "bob",
    "role_id": "role-3",
    "role_name": "VIP",
    "started_at": 1717000000000,
    "next_billing": 1719592000000,
    "active": true
  },
  "group": { "tag": "mygroup", "credits_balance": 250.0, "member_count": 6 }
}
```

`subscription` is `null` for one-time products. The `group` field is the full updated group object (shortened here).

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You already have this role` | Role already assigned |
| 400 | `You already have this subscription` | Active subscription exists |
| 400 | `Product does not grant a role` | Product misconfigured |
| 400 | `Insufficient funds` | Not enough credits (response includes `required` and `available`) |
| 403 | `You must be a member to purchase this role` | Not a member |
| 404 | `Product not found` / `Role no longer exists` | Bad product or deleted role |
| 404 | `Group not found` | Group doesn't exist |

***

## Cancel a Subscription

### POST `/v2/groups/{tag}/products/{productid}/cancel`

**Auth:** required. Token permission: `credits:manage`.

Schedules your active subscription to end at the next billing date. You keep the role until then.

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/products/prod-1/cancel?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "status": "Cancellation scheduled",
  "cancel_at": 1719592000000,
  "subscription": {
    "id": "sub-1",
    "group_tag": "mygroup",
    "product_id": "prod-1",
    "product_name": "VIP",
    "username": "bob",
    "role_id": "role-3",
    "role_name": "VIP",
    "started_at": 1717000000000,
    "next_billing": 1719592000000,
    "cancel_at": 1719592000000,
    "active": true
  }
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Active subscription not found` | No active subscription for this product |
| 404 | `Group not found` | Group doesn't exist |

***

## Check Product Ownership

### GET `/v2/groups/{tag}/products/{productid}/owners/{username}`

No authentication needed. Checks whether a user owns the product's role. `{username}` accepts a username or user ID.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/products/prod-1/owners/bob"
```

**Example response (200):**

```json
{
  "owned": true,
  "username": "bob",
  "group_tag": "mygroup",
  "product": { "id": "prod-1", "name": "VIP", "price_credits": 100.0, "subscription": true },
  "subscription": null
}
```

`subscription` is the user's active subscription for this product, or `null`.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Product not found` | Product ID doesn't exist |
| 404 | `Group not found` | Group doesn't exist |

***

## List My Subscriptions

### GET `/v2/groups/products/subscriptions/mine`

**Auth:** required. Token permission: `groups:view`.

Returns your active subscriptions across all groups.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/products/subscriptions/mine?auth=YOUR_TOKEN"
```

**Example response (200):** an array of subscription objects, as shown above. Empty array if you have none.
