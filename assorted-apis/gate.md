# Gate

Gate is a link shortener integrated with rotur. It lets a logged-in rotur user
create, rename and delete short links, and redirects visitors from a short slug
to the destination URL.

The API can be used same-origin (from the gate dashboard, via a session cookie)
or **across site boundaries** from any other web app using an `Authorization`
header. This page focuses on the cross-site flow.

## Base URL

All endpoints below are relative to the gate deployment, e.g.
`https://gate.rotur.dev`. Replace this with your actual gate host.

## Authentication

Gate sessions are created from a standard rotur token. A session is identified
by a `session_id`, which you can supply in one of two ways:

| Context | How the session is sent |
| --- | --- |
| Same-origin (dashboard) | `session_id` cookie (HttpOnly), set automatically |
| Cross-origin (other sites) | `Authorization: Bearer <session_id>` header |

Browsers do not send gate's cookie to other origins, so cross-site callers must
use the `Authorization` header.

### Getting a session

1. Obtain a rotur token using the standard [rotur.dev/auth](rotur.dev-auth.md)
   flow.
2. Exchange the token for a gate session:

```http
GET /api/auth?v=<rotur-token>
```

```json
{
  "ok": true,
  "token": "<rotur-token>",
  "session_id": "<session-id>"
}
```

Store `session_id` and send it on every authenticated request:

```http
GET /api/me
Authorization: Bearer <session-id>
```

> The `Bearer ` prefix is optional — gate also accepts the raw `session_id` as
> the `Authorization` value.

### CORS

Gate responds to cross-origin requests with:

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: GET, POST, PUT, PATCH, DELETE, OPTIONS, HEAD
Access-Control-Allow-Headers: Authorization, Content-Type
```

`Authorization` is listed explicitly because the `*` wildcard does **not** cover
it per the Fetch standard. Preflight (`OPTIONS`) requests to `/api/*` return
`204 No Content` with these headers.

Because authentication uses the `Authorization` header (not cookies), you do not
need `credentials: "include"` in `fetch`.

### Errors

Authenticated `/api/*` endpoints return `401` with a JSON body when the session
is missing or invalid:

```json
{ "ok": false, "error": "not authenticated" }
```

## Endpoints

### `GET /api/auth`

Exchange a rotur token for a gate session. See [Getting a session](#getting-a-session).

| Query | Description |
| --- | --- |
| `v` | A valid rotur token |

### `GET /api/me`

Returns the authenticated user.

```json
{
  "id": "<user-id>",
  "username": "mist",
  "subscription": "max",
  "canRename": true,
  "linkLimit": 100
}
```

### `GET /api/links`

Returns the authenticated user's links as an array of link objects.

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

### `POST /api/link`

Creates a short link.

| Query | Description |
| --- | --- |
| `to` | Destination URL (max 2000 chars, `http`/`https` only) |

Returns the created link object. Errors: `400` for an invalid destination or
when the user's link limit is reached.

### `POST /api/link/rename`

Renames a link the caller owns.

| Query | Description |
| --- | --- |
| `id` | The link slug |
| `newName` | The new slug (max 100 chars) |

Returns `{ "ok": true }`. Errors: `404` if not found, `403` if not the owner.

### `DELETE /api/link`

Deletes a link the caller owns.

| Query | Description |
| --- | --- |
| `id` | The link slug |

Returns `{ "ok": true }`. Errors: `404` if not found, `403` if not the owner.

### `POST /api/logout`

Ends the current session. Send the session via cookie or `Authorization`.

### `GET /:id`

Public redirect. Visiting `https://gate.rotur.dev/<slug>` issues a `302` to the
link's destination and increments its view count. Appending `.json`
(`/<slug>.json`) returns the link object instead of redirecting.

## Example

```javascript
const GATE = "https://gate.rotur.dev";

// 1. exchange a rotur token for a gate session
const auth = await fetch(`${GATE}/api/auth?v=${roturToken}`).then(r => r.json());
const session = auth.session_id;

// 2. call the API from any origin using the Authorization header
const me = await fetch(`${GATE}/api/me`, {
  headers: { Authorization: `Bearer ${session}` },
}).then(r => r.json());

// 3. create a link
const link = await fetch(`${GATE}/api/link?to=${encodeURIComponent("https://example.com")}`, {
  method: "POST",
  headers: { Authorization: `Bearer ${session}` },
}).then(r => r.json());
```
