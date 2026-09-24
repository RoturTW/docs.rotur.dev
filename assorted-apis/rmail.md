# rMail

rMail is Rotur's mail service: users send each other messages (rmails) with subjects, threads, labels and attachments. [mail.rotur.dev](https://mail.rotur.dev) is the official client, and everything it does goes through the public API on this page, so you can build your own client.

> **Base URL:** `https://mail.rotur.dev/api/v1`
>
> **Auth:** An `Authorization: Bearer <validator>` header on every request, where the validator is generated for the `rotur-mail` key. Your Rotur token is never sent to rMail. See [Authentication](#authentication).

This page covers the essentials. The full reference for every endpoint is in the [roturMail repository docs](https://git.rotur.dev/rotur/mail/src/branch/main/docs), and the running service describes itself at `GET /api/v1/openapi.json` and `GET /api/v1/docs`.

## Concepts

### Authentication

rMail uses [validators](validators/README.md) instead of your Rotur token.

1. Generate a validator for the `rotur-mail` key with your Rotur token: `GET https://api.rotur.dev/generate_validator?key=rotur-mail`.
2. Send the returned `validator` string as `Authorization: Bearer <validator>` on every rMail request.
3. rMail checks it with Rotur on each request. There are no cookies or sessions.

A validator lasts 5 minutes. When a request returns `401`, generate a new one and retry. Call `GET /me` on load to check that your validator is still accepted.

### Requests and responses

Inputs are passed as query-string parameters on every method, including `POST` and `PATCH`. Responses are JSON.

```json
{ "ok": true, "data": { } }
```

```json
{ "ok": false, "error": { "code": "not_found", "message": "rmail not found" } }
```

List endpoints add a `meta` object and accept `page` (default `1`) and `per_page` (default `50`, up to `200`):

```json
{ "ok": true, "data": [ ], "meta": { "page": 1, "per_page": 50, "total": 0, "total_pages": 0 } }
```

| Status | Example `code` |
| --- | --- |
| `400` | `bad_request` |
| `401` | `unauthorized` |
| `403` | `forbidden`, `banned`, `limit_reached` |
| `404` | `not_found` |
| `409` | `conflict` |
| `429` | `rate_limited` |
| `500` | `server_error` |

The report and undo actions use a flatter envelope: `{ "ok": true, ... }`, with errors as `{ "ok": false, "error": "<message>" }`.

### Limits

- **Storage:** measured in bytes and scaled by subscription tier. `GET /me` returns `used_bytes`, `max_bytes` and `can_send`. When storage is full, sending returns `403 limit_reached`.
- **Size:** a subject is up to 100 characters, and the subject and body together up to 50,000 characters. Up to 10 attachments per rmail.
- **Rate:** up to 5 rmails in any 60 seconds, and at least 15 seconds between sends. Going faster starts a cool-down of 10, 30, 90, 180 and then 300 seconds, escalating with repeat offenses, and sending returns `429 rate_limited`. `GET /ratelimit` shows the current state.

## GET `/me`

Returns the signed-in user with their storage use, unread and starred counts, and settings.

**Auth:** Required.

### Example

```http
GET /api/v1/me
Authorization: Bearer <validator>
```

**Response `200`:**

```json
{
  "ok": true,
  "data": {
    "id": "1234",
    "username": "alice",
    "display_name": "alice",
    "subscription": "plus",
    "avatar_url": "https://avatars.rotur.dev/alice",
    "auth_key": "rotur-mail",
    "rmail_count": 42,
    "max_rmails": 1000,
    "used_bytes": 1048576,
    "max_bytes": 104857600,
    "unread_count": 3,
    "starred_count": 5,
    "can_send": true,
    "is_admin": false,
    "is_banned": false,
    "settings": {
      "attachment_upload_url": "https://chats.mistium.com",
      "read_receipts": true,
      "encryption_enabled": false,
      "has_encryption_key": false
    }
  }
}
```

### Errors

| Status | When |
| --- | --- |
| `401` | The validator is missing, invalid or expired |
| `404` | The user's profile cannot be resolved |

## GET `/rmails`

Lists your rmails, filtered and paginated.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `box` | query | string | No | `inbox`, `sent`, `archive`, `trash`, `all` or `label:<name>`. Defaults to `all`. |
| `label` | query | string | No | Only rmails with this label |
| `q` | query | string | No | Case-insensitive match on body, subject, sender or recipient |
| `status` | query | string | No | `active`, `archived`, `trashed` or `draft` |
| `from` | query | string | No | Sender username |
| `to` | query | string | No | Recipient username |
| `starred` | query | boolean | No | `true` returns only starred rmails |
| `unread` | query | boolean | No | `true` returns only unread rmails |
| `sort` | query | string | No | `date` (default), `from`, `to` or `subject` |
| `order` | query | string | No | `desc` (default, newest first) or `asc` |
| `page`, `per_page` | query | integer | No | Pagination |

### Example

```http
GET /api/v1/rmails?box=inbox&unread=true
Authorization: Bearer <validator>
```

**Response `200`:** a paginated list of rmails. Each rmail looks like this:

```json
{
  "id": "uuid",
  "subject": "Hello",
  "body": "the message text",
  "from": { "username": "alice", "id": "1", "avatar_url": "…" },
  "to": { "username": "bob", "id": "2", "avatar_url": "…" },
  "mailbox": "inbox",
  "labels": ["work"],
  "is_read": false,
  "is_starred": false,
  "is_reply": false,
  "reply_to": null,
  "replies": [],
  "replies_count": 0,
  "attachments": [],
  "created_at": 1718900000000,
  "is_encrypted": false
}
```

The full object has more fields for threads, editing, expiry and read receipts; see the repository's `objects.md`.

### Errors

| Status | When |
| --- | --- |
| `400` | `box` is not one of the values above. Use `GET /drafts` for drafts. |

## GET `/rmails/:id`

Returns one rmail.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The rmail ID |

### Errors

| Status | When |
| --- | --- |
| `404` | You have no rmail with this ID |

## POST `/rmails`

Sends an rmail. It is stored in your Sent box and the recipient's Inbox, and the recipient is notified over the WebSocket.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `to` | query | string | Yes | Recipient username |
| `subject` | query | string | Yes | Up to 100 characters |
| `body` | query | string | No | The message. Subject and body together are up to 50,000 characters. |
| `reply_to` | query | string | No | A thread root ID, to add this rmail to that thread |
| `attachments` | query | string | No | A JSON array of attachments, up to 10 |
| `send_at` | query | integer | No | Delivery time in Unix milliseconds, at least about 5 seconds ahead. The rmail waits in your `scheduled` box until then. |
| `expires_at` | query | integer | No | Time in Unix milliseconds when the rmail is deleted from both mailboxes |
| `burn_after_read` | query | boolean | No | `true` deletes the rmail shortly after the recipient reads it |
| `encrypted` | query | boolean | No | `true` if `body` is an end-to-end encrypted envelope |

### Example

```http
POST /api/v1/rmails?to=bob&subject=Hello&body=Hi%20Bob
Authorization: Bearer <validator>
```

**Response `201`:** `{ "ok": true, "data": <rmail> }`

You can undo a sent rmail within 30 seconds with `POST /rmails/:id/undo`, which also refunds the rate-limit cost.

### Errors

| Status | When |
| --- | --- |
| `400` | `to` or `subject` is missing, the subject or message is too long, there are more than 10 attachments, or you sent to yourself without `reply_to` |
| `403` | Your storage is full (`limit_reached`) or you are banned from rMail (`banned`) |
| `404` | The recipient does not exist |
| `429` | You are sending too fast (`rate_limited`) |

## DELETE `/rmails/:id`

Permanently deletes an rmail from your mailbox. Deleting a thread root also deletes its replies. Deleting a scheduled rmail cancels it.

**Auth:** Required.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The rmail ID |

**Response `204`:** no body.

### Errors

| Status | When |
| --- | --- |
| `404` | You have no rmail with this ID |

## Other endpoints

All paths are under `/api/v1` and need the same `Authorization` header, except `GET /users/:username/avatar`, which is a public redirect.

| Area | Endpoints |
| --- | --- |
| Rmails | `PATCH /rmails/:id` (read, starred, labels), `POST /rmails/:id/read`, `/unread`, `/star`, `/unstar`, `/archive`, `/unarchive`, `/trash`, `/restore`, `POST /rmails/:id/edit`, `POST /rmails/:id/forward`, `POST /rmails/:id/report`, `POST /rmails/:id/undo` |
| Threads | `GET /rmails/:id/thread`, `GET /rmails/:id/replies`, `POST /rmails/:id/reply` (alias `POST /rmails/:id/replies`), `DELETE /rmails/:id/replies/:replyId`, `POST /rmails/:id/participants`, `DELETE /rmails/:id/participants/:username` |
| Drafts | `GET /drafts`, `POST /drafts`, `DELETE /drafts/:id` |
| Labels | `GET /labels`, `POST /labels`, `GET /labels/:name`, `PATCH /labels/:name`, `DELETE /labels/:name`, `GET /labels/:name/rmails` |
| Mailboxes | `GET /mailboxes`, `GET /mailboxes/:box/rmails` |
| Search | `GET /search` |
| Users | `GET /users/:username`, `GET /users/:username/avatar`, `GET /users/:username/key` |
| Settings | `GET /settings`, `PATCH /settings` |
| Blocking | `GET /blocked`, `PUT /blocked`, `POST /blocked/:username`, `DELETE /blocked/:username` |
| Encryption | `GET /encryption/keys`, `POST /encryption/keys`, `DELETE /encryption/keys` |
| Usage | `GET /stats`, `GET /ratelimit` |

## Legacy packet commands

{% hint style="warning" %}
This section documents the older packet-based rMail interface used from roturTW. It is not part of the current rMail API above. Use the HTTP API for new clients.
{% endhint %}

Clients sent command packets and received responses in the same format. Every request looked like this:

```js
{
  "cmd": "pmsg",
  "val": {
    "source": "client_identifier",
    "command": "omail_command",
    "payload": {
      // command-specific data
    }
  }
}
```

- `cmd`: always `"pmsg"`.
- `val.source`: an identifier for the requesting client.
- `val.command`: the rMail command.
- `val.payload`: the command's data.

Responses came from `sys-rotur` in the `roturTW` room, with the result in `val.payload`:

```js
{
  "cmd": "pmsg",
  "val": {
    "username": "sys-rotur",
    "source_command": "omail_getinfo",
    "target": "client_identifier",
    "payload": [ ... ]
  },
  "timestamp": 1715054321000,
  "origin": {
    "username": "sys-rotur"
  },
  "room": "roturTW"
}
```

| Command | Request `payload` | Response `payload` |
| --- | --- | --- |
| `omail_getinfo` | None | An array of rMail summaries: `{ "recipient", "title", "timestamp", "from" }` |
| `omail_send` | `{ "recipient", "title", "body" }` | `"Successfully Sent Omail"`, or `"Please wait before sending another rMail. Rate limit is 60 seconds."` |
| `omail_delete` | The 1-based index of the rMail, or `"all"` | `"Deleted Successfully"` |
| `omail_total` | None | The number of rMails |
| `omail_getid` | The 1-based index of the rMail | `[index, { "body", "info": { "recipient", "title", "timestamp", "from" } }]` |

Each rMail was stored as:

```js
{
  "body": "Content of the rMail",
  "info": {
    "recipient": "username",
    "title": "rMail subject",
    "timestamp": 1715054321000,
    "from": "sender_username"
  }
}
```

Limits and behavior:

- One rMail every 60 seconds per user.
- Payloads up to 50 KB, and titles up to 100 characters.
- rMails were stored in a JSON file (`rmails.json`) on the server.
- A request for an rMail that does not exist returned an empty object.
- An unrecognized command got no response.
- An invalid deletion index was ignored.
