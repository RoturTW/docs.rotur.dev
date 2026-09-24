# Products and subscriptions

Groups can sell **role products**: a member pays credits and gets a role, along with that role's benefits. A product is either a one-time purchase or a subscription that renews automatically. Sales go into the group's `credits_balance`.

Timestamps on subscriptions (`started_at`, `next_billing`, `cancel_at`) are in Unix milliseconds.

## GET `/v2/groups/{tag}/products`

List a group's products, in no particular order.

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/mygroup/products
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of products, or `[]` if there are none. `role_granted_id`, `role_name`, `benefit_granted`, `frequency`, and `period` are left out when empty.

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

## POST `/v2/groups/{tag}/products`

Create a product that grants a role.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.roles.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | query | string | Yes | Up to 50 characters |
| `description` | query | string | No | Up to 200 characters |
| `price_credits` | query | number | Yes | Price in credits. Must be positive |
| `role_id` | query | string | Yes | The role to grant. Can't be the Owner role |
| `subscription` | query | string | No | `true` for a subscription. Default `false` |
| `frequency` | query | integer | No | Bill every N periods. Default `1`. Subscriptions only |
| `period` | query | string | No | `day`, `week`, `month`, or `year`. Default `month`. Subscriptions only |

### Example

```http
POST /v2/groups/mygroup/products?name=VIP&price_credits=100&role_id=role-3&subscription=true&period=month
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the new product, in the same shape as the list response.

### Errors

| Status | When |
| --- | --- |
| `400` | `name` is missing (`Name is required`) or over 50 characters (`Name length exceeded`) |
| `400` | `description` is over 200 characters (`Description length exceeded`) |
| `400` | `price_credits` is missing, not a number, or not positive (`Invalid price`) |
| `400` | `frequency` isn't a positive integer (`Invalid frequency`) or `period` isn't an allowed value (`Invalid period`) |
| `400` | `role_id` is missing (`Role ID is required`) |
| `400` | `role_id` is the Owner role (`Owner role cannot be sold`) |
| `403` | You lack `groups.roles.manage` (`You don't have permission to manage role products`) |
| `404` | No role has that ID in this group (`Role not found`) |

## DELETE `/v2/groups/{tag}/products/{productid}`

Delete a product. Members who bought it keep the role. Active subscriptions to it stop at their next billing date without charging again.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.roles.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `productid` | path | string | Yes | The product's `id` |

### Example

```http
DELETE /v2/groups/mygroup/products/prod-1
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Product deleted"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.roles.manage` (`You don't have permission to manage role products`) |
| `404` | No product has that ID in this group (`Product not found`) |

## POST `/v2/groups/{tag}/products/{productid}/purchase`

Buy a product. The price is charged to you as a `group_role_purchase` transaction, you get the role, and the credits go to the group.

For a subscription, you're charged again each period as a `group_role_subscription` transaction. If you can't afford a renewal, the subscription ends and the role is removed.

**Auth:** Required. Sub-tokens need `credits:manage`. You must be a member of the group.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `productid` | path | string | Yes | The product's `id` |

### Example

```http
POST /v2/groups/mygroup/products/prod-1/purchase
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** `subscription` is `null` for one-time products. `group` is the full updated [group object](README.md#group), shortened here.

```json
{
  "message": "Product purchased",
  "product": {
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

### Errors

| Status | When |
| --- | --- |
| `400` | You already have the role (`You already have this role`) |
| `400` | You already have an active subscription to this product (`You already have this subscription`) |
| `400` | The product has no role (`Product does not grant a role`) |
| `400` | You don't have enough credits (`Insufficient funds`). The body also has `required` and `available` |
| `403` | You aren't a member (`You must be a member to purchase this role`) |
| `404` | No product has that ID (`Product not found`), or its role was deleted (`Role no longer exists`) |

## POST `/v2/groups/{tag}/products/{productid}/cancel`

Cancel your subscription to a product. It ends at the next billing date instead of renewing, and you keep the role until then.

**Auth:** Required. Sub-tokens need `credits:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `productid` | path | string | Yes | The product's `id` |

### Example

```http
POST /v2/groups/mygroup/products/prod-1/cancel
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

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

### Errors

| Status | When |
| --- | --- |
| `404` | You have no active subscription to this product (`Active subscription not found`) |

## GET `/v2/groups/{tag}/products/{productid}/owners/{username}`

Check whether a user has the role a product grants, however they got it.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `productid` | path | string | Yes | The product's `id` |
| `username` | path | string | Yes | A username or user ID. It's echoed back as `username` |

### Example

```http
GET /v2/groups/mygroup/products/prod-1/owners/bob
```

**Response `200`:** `product` is the full product object, shortened here. `subscription` is the user's active subscription to this product, or `null`.

```json
{
  "owned": true,
  "username": "bob",
  "group_tag": "mygroup",
  "product": { "id": "prod-1", "name": "VIP", "price_credits": 100.0, "subscription": true },
  "subscription": null
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | No product has that ID in this group (`Product not found`) |

## GET `/v2/groups/products/subscriptions/mine`

List your active subscriptions across all groups.

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/products/subscriptions/mine
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of subscription objects, in the same shape as in the purchase response, or `[]` if you have none.
