# Linking

Linking lets a program such as a desktop app, game or device get a Rotur token without running its own login. Your app shows a short code, the user enters it at [rotur.dev/link](https://rotur.dev/link) on any device, and your app collects a scoped sub-token.

> **Base URL:** `https://api.rotur.dev`
>
> **Auth:** Your app needs none. Only `POST /link/code`, which the link page calls, needs the user's token in the `Authorization: Bearer <token>` header (the legacy `auth` query parameter is also accepted).

Every endpoint is also available under `/v2/link` with the same paths. The `GET` endpoints are rate limited.

{% hint style="info" %}
Codes expire 10 minutes after they are created. If a code expires before the user finishes, request a new one.
{% endhint %}

## The flow

1. Your app calls `GET /link/code`, optionally with a name and the permissions it wants, and shows the code.
2. The user opens [rotur.dev/link](https://rotur.dev/link), signs in, enters the code and picks which permissions to grant. The page creates a sub-token with those permissions and attaches it to the code with `POST /link/code`.
3. Your app polls `GET /link/status`, or waits for the user to confirm in your app.
4. Your app calls `GET /link/user` once to collect the token.

The token you receive is always a [sub-token](tokens/README.md), never the user's main token.

## GET `/link/code`

Creates a new link code.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | query | string | No | Your app's name, shown to the user on the link page. Cut to 50 characters. |
| `permissions` | query | string | No | Comma-separated [permissions](tokens/permissions.md) your app is asking for. The user chooses the final set. `tokens:manage` is not allowed. |

### Example

```http
GET /link/code?name=My%20App&permissions=account:view,posts:view
```

**Response `200`:**

```json
{
  "code": "A1B2C3",
  "expires_in": 600
}
```

The code is 6 uppercase hex characters (`0`–`9`, `A`–`F`). `expires_in` is in seconds.

### Errors

| Status | When |
| --- | --- |
| `400` | `permissions` contains an unknown permission or `tokens:manage` |

## GET `/link/info`

Returns what an app asked for with a code. The link page uses it to show the request to the user.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `code` | query | string | Yes | The link code |

### Example

```http
GET /link/info?code=A1B2C3
```

**Response `200`:**

```json
{
  "name": "My App",
  "permissions": ["account:view", "posts:view"],
  "linked": false
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | The code is unknown or expired (`No auth code found`) |

## POST `/link/code`

Attaches one of the signed-in user's sub-tokens to a code. The link page at rotur.dev/link calls this; you only need it if you build your own link page.

**Auth:** Required. Sub-tokens need `account:settings`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `code` | query | string | Yes | The link code |
| `token` | body | string | Yes | A sub-token value (`rotur_st_…`) that belongs to the signed-in user. The main token is rejected. |

### Example

```http
POST /link/code?code=A1B2C3
Authorization: Bearer <token>
Content-Type: application/json

{
  "token": "rotur_st_xYz123..."
}
```

**Response `200`:**

```json
{
  "linked": true,
  "token_id": "st_abc123"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `token` is missing or is the main token (`A scoped token is required to link a device`) |
| `400` | `token` is not an active sub-token of this account (`Token does not belong to this account`) |
| `404` | The code is unknown or expired (`No auth code found`) |

## GET `/link/status`

Checks whether the user has finished linking. It does not use up the code, so you can poll it.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `code` | query | string | Yes | The link code |

### Example

```http
GET /link/status?code=A1B2C3
```

**Response `200`:**

```json
{ "status": "linked" }
```

### Errors

| Status | When |
| --- | --- |
| `404` | Not linked yet, expired or unknown. The body is `{ "status": "not found" }`. |

## GET `/link/user`

Returns the linked token and deletes the code.

**Auth:** None.

{% hint style="warning" %}
A successful call deletes the code, so you get the token exactly once. Store it.
{% endhint %}

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `code` | query | string | Yes | The link code |

### Example

```http
GET /link/user?code=A1B2C3
```

**Response `200`:**

```json
{
  "linked": true,
  "token": "rotur_st_xYz123..."
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | Not linked yet, expired or unknown. The body is `{ "linked": false, "token": "" }`. |

Once you have the token, use it with other Rotur services or to [get the user's data](../deprecated/authentication/get-user-data.md).
