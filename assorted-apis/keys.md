# Keys

Keys are sellable access passes. You create a key, optionally give it a price or a subscription, and other users can buy it. Your app can then check whether a user owns your key.

> **Base URL:** `https://api.rotur.dev/`
>
> All v1 key routes use the **GET** method and take their parameters as URL query parameters. The key's `id` in each path is the key string itself.
>
> **Manage your keys with the GUI:**\
> [https://rotur.dev/key-manager](https://rotur.dev/key-manager)

{% hint style="info" %}
Authenticated endpoints accept your token in an `Authorization: Bearer` header (preferred). The `auth` query parameter is also accepted as a legacy fallback. Sub-tokens need the `keys:manage` permission for everything except `/keys/mine` (which needs `keys:view`). `/keys/get` and `/keys/check` are public.
{% endhint %}

***

### GET `/keys/create`

Create a new key. You are added to it automatically as its creator.

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.
* `name`: a name for the key (required)
* `description`: text stored in the key's `data` field (optional)
* `price`: price in credits, whole number, defaults to 0 (optional)
* `subscription`: set to `true` or `1` to make this a subscription key (optional)
* `frequency`: how many periods between charges, defaults to 1 (optional)
* `period`: `day`, `week`, `month`, or `year`, defaults to `month` (optional)

**Example:**

```http
GET /keys/create?name=My%20App%20Pro&price=10
```

**Example Response (200):**

```json
{
  "status": "Key created successfully",
  "key": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6",
  "type": "standard",
  "price": 10
}
```

Subscription keys also include a `subscription` object with `active`, `frequency`, `period`, and `next_billing`.

**Common Errors:**

| Status | Condition |
|---|---|
| 400 | `name` is missing |
| 400 | You have reached your key limit (depends on your subscription tier) |

***

### GET `/keys/mine`

List every key you have access to, whether you created it or bought it.

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Example:**

```http
GET /keys/mine
```

**Example Response (200):**

A JSON array of key objects.

```json
[
  {
    "key": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6",
    "name": "My App Pro",
    "price": 10,
    "type": "standard",
    "total_income": 30,
    "users": {
      "misty": { "time": 1715512345 }
    },
    "creator": "misty"
  }
]
```

For keys you did not create, `total_income` is hidden (reported as 0).

***

### GET `/keys/get/<id>`

Get public info about a key. No authentication needed.

**Path Parameter:**

* `<id>`: the key string

**Example:**

```http
GET /keys/get/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
```

**Example Response (200):**

```json
{
  "key": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6",
  "name": "My App Pro",
  "price": 10,
  "type": "standard"
}
```

**Common Errors:**

| Status | Condition |
|---|---|
| 404 | Key not found |

***

### GET `/keys/check/<username>`

Check whether a user owns a specific key. No authentication needed.

**Path Parameter:**

* `<username>`: the username to check

**Query Parameters:**

* `key`: the key string to check against (required)

**Example:**

```http
GET /keys/check/misty?key=a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
```

**Example Response (200):**

```json
{
  "owned": true,
  "username": "misty",
  "key": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6"
}
```

**Common Errors:**

| Status | Condition |
|---|---|
| 400 | `key` is missing |

***

### GET `/keys/name/<id>`

Rename a key you created.

**Path Parameter:**

* `<id>`: the key string

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.
* `name`: the new name (required)

**Example:**

```http
GET /keys/name/a1b2c3d4?name=New%20Name
```

**Example Response (200):**

```json
{ "status": "Key name updated successfully" }
```

**Common Errors:**

| Status | Condition |
|---|---|
| 400 | `name` is missing |
| 403 | You are not the key's creator |
| 404 | Key not found |

***

### GET `/keys/update/<id>`

Update a single field on a key you created.

**Path Parameter:**

* `<id>`: the key string

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.
* `key`: the field to update: `name`, `price`, `data`, `type`, or `webhook` (required)
* `data`: the new value. Valid JSON is parsed (so `5` becomes a number), anything else is stored as a string (optional)

**Example:**

```http
GET /keys/update/a1b2c3d4?key=price&data=5
```

**Example Response (200):**

```json
{ "status": "Key updated successfully" }
```

**Common Errors:**

| Status | Condition |
|---|---|
| 403 | `key` parameter is missing |
| 403 | You are not the key's creator |
| 404 | Key not found |

***

### GET `/keys/buy/<id>`

Buy access to a key. Also accepts POST. The price is taken from your credits and the creator receives 90% of it (a 10% tax applies). Buying a subscription key sets up recurring billing.

**Path Parameter:**

* `<id>`: the key string

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Example:**

```http
GET /keys/buy/a1b2c3d4
```

**Example Response (200):**

```json
{ "message": "Key purchased successfully" }
```

**Common Errors:**

| Status | Condition |
|---|---|
| 400 | Key is not for sale |
| 400 | You already have access to this key |
| 400 | Insufficient balance |
| 404 | Key not found |

***

### GET `/keys/cancel/<id>`

Give up your access to a key. Also accepts POST. For subscription keys, your access continues until the next billing date and is removed then. For standard keys, access is removed immediately.

**Path Parameter:**

* `<id>`: the key string

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Example:**

```http
GET /keys/cancel/a1b2c3d4
```

**Example Response (200):**

```json
{ "status": "Cancellation scheduled", "cancel_at": 1718191234000 }
```

For non-subscription keys the response is:

```json
{ "status": "Cancelled" }
```

**Common Errors:**

| Status | Condition |
|---|---|
| 400 | The subscription has no next billing date |
| 404 | Key not found, or you do not have this key |

***

### GET `/keys/revoke/<id>`

Remove a user's access to a key you created.

**Path Parameter:**

* `<id>`: the key string

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.
* `user`: the username to remove (required)

**Example:**

```http
GET /keys/revoke/a1b2c3d4?user=misty
```

**Example Response (200):**

```json
{ "status": "Key access revoked successfully" }
```

**Common Errors:**

| Status | Condition |
|---|---|
| 400 | Target user not found |
| 400 | You cannot revoke access from the key creator |
| 403 | You are not the key's creator |
| 404 | Key not found |

***

### GET `/keys/delete/<id>`

Delete a key you created. Also accepts DELETE.

**Path Parameter:**

* `<id>`: the key string

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Example:**

```http
GET /keys/delete/a1b2c3d4
```

**Example Response (200):**

```json
{ "status": "Key deleted successfully" }
```

**Common Errors:**

| Status | Condition |
|---|---|
| 403 | You are not the key's creator |
| 404 | Key not found |

***

### GET `/keys/admin_add/<id>`

Manually give a user access to a key you created, without them paying.

**Path Parameter:**

* `<id>`: the key string

**Query Parameters:**

* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.
* `user`: the username to add (required, `username` also accepted)

**Example:**

```http
GET /keys/admin_add/a1b2c3d4?user=misty
```

**Example Response (200):**

```json
{ "status": "User added to key successfully" }
```

**Common Errors:**

| Status | Condition |
|---|---|
| 400 | Target user missing or not found |
| 403 | You are not the key's creator |
| 404 | Key not found |

***

### GET `/keys/admin_remove/<id>`

Manually remove a user from a key you created. Same parameters and errors as `admin_add`.

**Example:**

```http
GET /keys/admin_remove/a1b2c3d4?user=misty
```

**Example Response (200):**

```json
{ "status": "User removed from key successfully" }
```

***

## v2 Equivalents

The same handlers are exposed RESTfully under `https://api.rotur.dev/v2/keys`:

| v1 | v2 |
|---|---|
| GET `/keys/create` | POST `/v2/keys` |
| GET `/keys/mine` | GET `/v2/keys/mine` |
| GET `/keys/get/:id` | GET `/v2/keys/:id` |
| GET `/keys/check/:username` | GET `/v2/keys/check/:username` |
| GET `/keys/name/:id` | PATCH `/v2/keys/:id/name` |
| GET `/keys/update/:id` | PATCH `/v2/keys/:id` |
| GET `/keys/buy/:id` | POST `/v2/keys/:id/buy` |
| GET `/keys/cancel/:id` | POST `/v2/keys/:id/cancel` |
| GET `/keys/revoke/:id` | POST `/v2/keys/:id/revoke` |
| GET `/keys/delete/:id` | DELETE `/v2/keys/:id` |
| GET `/keys/admin_add/:id` | POST `/v2/keys/:id/members` |
| GET `/keys/admin_remove/:id` | DELETE `/v2/keys/:id/members` |

Parameters are still passed as query parameters in v2.
