# Keys

Keys are access passes you can sell. You create a key, optionally give it a price or make it a subscription, and other users buy it. Your app then checks whether a user owns the key.

> **Base URL:** `https://api.rotur.dev`
>
> **Auth:** Send your token in the `Authorization: Bearer <token>` header. The legacy `auth` query parameter is also accepted. Sub-tokens need `keys:manage` for every authenticated endpoint except `GET /keys/mine`, which needs `keys:view`. `GET /keys/get/:id` and `GET /keys/check/:username` are public.

You can also manage your keys in the browser at [rotur.dev/key-manager](https://rotur.dev/key-manager).

The v1 routes below all use `GET` (some also accept another method) and take their parameters from the query string. The `:id` in each path is the key string itself, a 32-character hex value. See [v2 equivalents](#v2-equivalents) for the RESTful paths.

***

## GET `/keys/create`

Creates a key. You are added to it as its creator.

**Auth:** Required. Sub-tokens need `keys:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | query | string | Yes | The key's name |
| `description` | query | string | No | Text stored in the key's `data` field |
| `price` | query | integer | No | Price in credits. Defaults to `0`; a negative or non-numeric value is treated as `0`. |
| `subscription` | query | string | No | `true` or `1` makes this a subscription key |
| `frequency` | query | integer | No | Number of periods between charges. Defaults to `1`. |
| `period` | query | string | No | `day`, `week`, `month` or `year`. Defaults to `month`. |

### Example

```http
GET /keys/create?name=My%20App%20Pro&price=10
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "status": "Key created successfully",
  "key": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6",
  "type": "standard",
  "price": 10
}
```

`price` is left out when it is `0`. Subscription keys also return a `subscription` object with `active`, `frequency`, `period` and `next_billing`.

### Errors

| Status | When |
| --- | --- |
| `400` | `name` is missing |
| `400` | You have reached the key limit for your subscription tier |

The number of keys you can create depends on your subscription tier:

| Tier | Keys |
| --- | --- |
| Free | 5 |
| Lite | 10 |
| Plus | 20 |
| Pro | 50 |
| Max | 500 |

***

## GET `/keys/mine`

Lists every key you have access to, whether you created it or bought it.

**Auth:** Required. Sub-tokens need `keys:view`.

### Example

```http
GET /keys/mine
Authorization: Bearer <token>
```

**Response `200`:**

An array of keys.

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

`users` maps each username to when they got access (Unix seconds) and, for buyers, the `price` they paid and billing fields. For keys you did not create, `data` and `total_income` are left out.

***

## GET `/keys/get/:id`

Returns public information about a key.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The key string |

### Example

```http
GET /keys/get/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
```

**Response `200`:**

```json
{
  "key": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6",
  "name": "My App Pro",
  "price": 10,
  "type": "standard"
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | The key does not exist |

***

## GET `/keys/check/:username`

Checks whether a user owns a key.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The user to check |
| `key` | query | string | Yes | The key string |

### Example

```http
GET /keys/check/misty?key=a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
```

**Response `200`:**

```json
{
  "owned": true,
  "username": "misty",
  "key": "a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `key` is missing |

***

## GET `/keys/name/:id`

Renames a key you created.

**Auth:** Required. Sub-tokens need `keys:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The key string |
| `name` | query | string | Yes | The new name |

### Example

```http
GET /keys/name/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6?name=New%20Name
Authorization: Bearer <token>
```

**Response `200`:**

```json
{ "status": "Key name updated successfully" }
```

### Errors

| Status | When |
| --- | --- |
| `400` | `name` is missing |
| `403` | You did not create the key |
| `404` | The key does not exist |

***

## GET `/keys/update/:id`

Updates one field on a key you created.

**Auth:** Required. Sub-tokens need `keys:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The key string |
| `key` | query | string | Yes | The field to update: `name`, `price`, `data`, `type` or `webhook` |
| `data` | query | string | No | The new value. If it is valid JSON it is parsed first, so `5` becomes a number. |

`price` needs a number. `name`, `data`, `type` and `webhook` need a string, so a value that parses as JSON (such as `123` or `true`) is ignored for those fields. An unknown field name is also ignored; the request still returns `200`.

Setting `price` to a negative number takes the key off sale. When a webhook is set, each purchase sends it a `POST` with a JSON body containing `username`, `key`, `price`, `content` and `timestamp`.

### Example

```http
GET /keys/update/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6?key=price&data=5
Authorization: Bearer <token>
```

**Response `200`:**

```json
{ "status": "Key updated successfully" }
```

### Errors

| Status | When |
| --- | --- |
| `403` | `key` is missing |
| `403` | You did not create the key |
| `404` | The key does not exist |

***

## GET `/keys/buy/:id`

Buys access to a key. Also accepts `POST`. The price is taken from your credits and the creator receives 90% of it. Buying a subscription key sets up recurring billing.

**Auth:** Required. Sub-tokens need `keys:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The key string |

### Example

```http
POST /keys/buy/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
Authorization: Bearer <token>
```

**Response `200`:**

```json
{ "message": "Key purchased successfully" }
```

### Errors

| Status | When |
| --- | --- |
| `400` | The key is not for sale (its price is negative) |
| `400` | You already have access to the key |
| `400` | Your balance is too low |
| `404` | The key does not exist |

***

## GET `/keys/cancel/:id`

Gives up your access to a key. Also accepts `POST`. For a subscription key, access continues until your next billing date and is removed then. For a standard key, access is removed immediately.

**Auth:** Required. Sub-tokens need `keys:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The key string |

### Example

```http
POST /keys/cancel/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
Authorization: Bearer <token>
```

**Response `200` (subscription key):**

```json
{ "status": "Cancellation scheduled", "cancel_at": 1718191234000 }
```

`cancel_at` is in Unix milliseconds.

**Response `200` (standard key):**

```json
{ "status": "Cancelled" }
```

### Errors

| Status | When |
| --- | --- |
| `400` | Your subscription has no next billing date |
| `404` | The key does not exist, or you do not have it |

***

## GET `/keys/revoke/:id`

Removes a user's access to a key you created.

**Auth:** Required. Sub-tokens need `keys:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The key string |
| `user` | query | string | Yes | The username to remove |

### Example

```http
GET /keys/revoke/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6?user=misty
Authorization: Bearer <token>
```

**Response `200`:**

```json
{ "status": "Key access revoked successfully" }
```

### Errors

| Status | When |
| --- | --- |
| `400` | `user` is missing or does not exist |
| `400` | `user` is the key's creator |
| `403` | You did not create the key |
| `404` | The key does not exist |

***

## GET `/keys/delete/:id`

Deletes a key you created. Also accepts `DELETE`.

**Auth:** Required. Sub-tokens need `keys:manage`.

{% hint style="warning" %}
Deleting a key removes every user's access to it, including buyers. It cannot be undone.
{% endhint %}

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The key string |

### Example

```http
DELETE /keys/delete/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
Authorization: Bearer <token>
```

**Response `200`:**

```json
{ "status": "Key deleted successfully" }
```

### Errors

| Status | When |
| --- | --- |
| `403` | You did not create the key |
| `404` | The key does not exist |

***

## GET `/keys/admin_add/:id`

Gives a user access to a key you created, without them paying.

**Auth:** Required. Sub-tokens need `keys:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The key string |
| `user` | query | string | Yes | The username to add. `username` is also accepted. |

### Example

```http
GET /keys/admin_add/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6?user=misty
Authorization: Bearer <token>
```

**Response `200`:**

```json
{ "status": "User added to key successfully" }
```

### Errors

| Status | When |
| --- | --- |
| `400` | `user` is missing or does not exist |
| `403` | You did not create the key |
| `404` | The key does not exist |

***

## GET `/keys/admin_remove/:id`

Removes a user from a key you created. It takes the same parameters and returns the same errors as `admin_add`.

**Auth:** Required. Sub-tokens need `keys:manage`.

### Example

```http
GET /keys/admin_remove/a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6?user=misty
Authorization: Bearer <token>
```

**Response `200`:**

```json
{ "status": "User removed from key successfully" }
```

***

## v2 equivalents

The same handlers are available under `https://api.rotur.dev/v2/keys`. Parameters are still passed in the query string.

| v1 | v2 |
| --- | --- |
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
