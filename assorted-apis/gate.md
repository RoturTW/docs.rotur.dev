# Gate

Gate is a link shortener tied to Rotur accounts. A signed-in Rotur user can create, rename and delete short links, and visitors to a short link are redirected to its destination. This page covers calling the Gate API from your own web app.

> **Base URL:** your Gate deployment, for example `https://gate.rotur.dev`
>
> **Auth:** A Gate session ID in the `Authorization: Bearer <session_id>` header. You get a session by exchanging a Rotur token with `GET /api/auth`. The Gate dashboard itself uses a `session_id` cookie instead.

## Concepts

### Sessions

Gate sessions are created from a Rotur token and identified by a `session_id`.

| Context | How the session is sent |
| --- | --- |
| Same origin (the Gate dashboard) | The `session_id` cookie (HttpOnly), set automatically |
| Other origins (your app) | `Authorization: Bearer <session_id>` |

Browsers do not send Gate's cookie to other origins, so apps on other sites must use the header. The `Bearer ` prefix is optional; Gate also accepts the bare `session_id`.

To get a session:

1. Get a Rotur token through [rotur.dev/auth](rotur.dev-auth.md).
2. Exchange it with `GET /api/auth` and store the returned `session_id`.
3. Send the `session_id` on every authenticated request.

### CORS

Gate answers cross-origin requests with:

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, PATCH, DELETE, OPTIONS, HEAD
Access-Control-Allow-Headers: Authorization, Content-Type
```

`Authorization` is listed explicitly because the `*` wildcard does not cover it under the Fetch standard. Preflight (`OPTIONS`) requests to `/api/*` return `204 No Content` with these headers. Because authentication uses a header rather than cookies, you do not need `credentials: "include"` in `fetch`.

### Errors

Authenticated `/api/*` endpoints return `401` when the session is missing or invalid:

```json
{ "ok": false, "error": "not authenticated" }
```

## GET `/api/auth`

Exchanges a Rotur token for a Gate session.

**Auth:** None. The Rotur token goes in `v`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `v` | query | string | Yes | A valid Rotur token |

### Example

```http
GET /api/auth?v=<rotur-token>
```

**Response `200`:**

```json
{
  "ok": true,
  "token": "<rotur-token>",
  "session_id": "<session-id>"
}
```

## GET `/api/me`

Returns the signed-in user and their link allowance.

**Auth:** Required. Gate session.

### Example

```http
GET /api/me
Authorization: Bearer <session-id>
```

**Response `200`:**

```json
{
  "id": "<user-id>",
  "username": "mist",
  "subscription": "max",
  "canRename": true,
  "linkLimit": 100
}
```

## GET `/api/links`

Returns your links.

**Auth:** Required. Gate session.

### Example

```http
GET /api/links
Authorization: Bearer <session-id>
```

**Response `200`:**

```json
[
  {
    "id": "abc123",
    "to": "https://example.com",
    "owner": "mist",
    "owner_id": "<user-id>",
    "created_at": 1782400000,
    "views": 4
  }
]
```

## POST `/api/link`

Creates a short link.

**Auth:** Required. Gate session.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `to` | query | string | Yes | Destination URL, `http` or `https` only, up to 2000 characters |

### Example

```http
POST /api/link?to=https%3A%2F%2Fexample.com
Authorization: Bearer <session-id>
```

**Response:** the new link, in the same shape as the items from `GET /api/links`.

### Errors

| Status | When |
| --- | --- |
| `400` | The destination is invalid |
| `400` | You have reached your link limit |

## POST `/api/link/rename`

Changes the slug of a link you own.

**Auth:** Required. Gate session.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | The current slug |
| `newName` | query | string | Yes | The new slug, up to 100 characters |

### Example

```http
POST /api/link/rename?id=abc123&newName=my-link
Authorization: Bearer <session-id>
```

**Response:**

```json
{ "ok": true }
```

### Errors

| Status | When |
| --- | --- |
| `403` | You do not own the link |
| `404` | The link does not exist |

## DELETE `/api/link`

Deletes a link you own.

**Auth:** Required. Gate session.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | query | string | Yes | The slug |

### Example

```http
DELETE /api/link?id=abc123
Authorization: Bearer <session-id>
```

**Response:**

```json
{ "ok": true }
```

### Errors

| Status | When |
| --- | --- |
| `403` | You do not own the link |
| `404` | The link does not exist |

## POST `/api/logout`

Ends the current session.

**Auth:** Required. Gate session, sent as the cookie or the `Authorization` header.

## GET `/:id`

Redirects a visitor to the link's destination.

**Auth:** None.

Visiting `https://gate.rotur.dev/<slug>` returns a `302` to the destination and adds one to the link's view count. Adding `.json` (`/<slug>.json`) returns the link object instead of redirecting.

## Example

```javascript
const GATE = "https://gate.rotur.dev";

// 1. Exchange a Rotur token for a Gate session
const auth = await fetch(`${GATE}/api/auth?v=${roturToken}`).then((r) => r.json());
const session = auth.session_id;

// 2. Call the API from any origin with the Authorization header
const me = await fetch(`${GATE}/api/me`, {
  headers: { Authorization: `Bearer ${session}` },
}).then((r) => r.json());

// 3. Create a link
const link = await fetch(`${GATE}/api/link?to=${encodeURIComponent("https://example.com")}`, {
  method: "POST",
  headers: { Authorization: `Bearer ${session}` },
}).then((r) => r.json());
```
