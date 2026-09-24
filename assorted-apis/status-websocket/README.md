# Status WebSocket

The status WebSocket is Rotur's real-time connection for presence. You join named rooms, see who else is in them, get live updates when their status, presence, or activities change, and send messages to a room or to one member. It replaces the legacy Rotur WebSocket: `gmsg` and `pmsg` cover the same room messaging.

You can also read and set your status over HTTP; see [HTTP endpoints](#http-endpoints).

## Connect

```
wss://api.rotur.dev/v2/status/ws
```

`wss://api.rotur.dev/status/ws`, `/ws`, and `/v2/ws` serve the same socket.

Every message in both directions is a JSON object with a `cmd` field. Each message you send can be up to 64 KB; a larger one closes the connection.

The server accepts up to 256 connections in total and 16 per IP address. Past either limit, the upgrade request is refused with HTTP `429` and `{"error": "too many websocket connections"}`.

## Authenticate

Send `auth` first. Until you do, every other command returns the `not authenticated` error, and if you haven't authenticated within 15 seconds of connecting, the server closes the connection.

```json
{
  "cmd": "auth",
  "key": "your_rotur_token"
}
```

`key` accepts a main account token or a sub-token. On success you receive `ready`:

```json
{
  "cmd": "ready",
  "user_id": "abc123",
  "username": "mist",
  "user": { "...": "your full user object, including sys.status" }
}
```

A sub-token needs `account:profile` to use `set_status`, `add_activity`, and `remove_activity`, and `gifts:view` to use `gift_subscribe`. Everything else works with any valid token.

Your status text and presence are saved to your account (`sys.status`). When your last connection closes they're kept, and they're restored the next time you authenticate. Activities aren't saved.

## Messages you send

### `join`

Join one or more rooms. `rooms` is a room name or an array of names.

```json
{
  "cmd": "join",
  "rooms": ["originChats", "originChats:general"]
}
```

Room names are 1–64 characters from `a-z`, `A-Z`, `0-9`, `_`, `-`, `:`, and `.`. A connection can be in up to 200 rooms. If any name in the list is invalid, already joined, or would go over the limit, you get an error and none of the rooms are joined.

For each room you receive `join_ok` followed by `room_state`:

```json
{ "cmd": "join_ok", "room": "originChats" }
```

Other members of the room receive `member_join`, unless your presence is `invisible`.

### `leave`

Leave one or more rooms. `rooms` takes the same forms as in `join`.

```json
{
  "cmd": "leave",
  "rooms": "originChats"
}
```

For each room you receive `leave_ok`:

```json
{ "cmd": "leave_ok", "room": "originChats" }
```

Other members receive `member_leave`. If another of your connections is still in the room, they receive a `status_update` instead.

### `rooms`

List the rooms this connection is in.

```json
{ "cmd": "rooms" }
```

Reply:

```json
{
  "cmd": "rooms",
  "rooms": ["originChats", "originChats:general"]
}
```

### `room_state`

Get the current members of a room this connection is in. The reply is a `room_state` message (see [Messages you receive](#messages-you-receive)).

```json
{
  "cmd": "room_state",
  "room": "originChats"
}
```

### `gmsg`

Broadcast a value to everyone else in a room this connection is in. `val` can be any JSON value. Messages aren't stored; only connections that are in the room at that moment receive them, including members whose presence is `invisible`.

```json
{
  "cmd": "gmsg",
  "room": "originChats",
  "val": "hello everyone",
  "listener": "optional-echo-tag"
}
```

Other members receive a `gmsg`. You don't receive your own broadcast; instead you get `gmsg_ok`, with `listener` echoed back if you sent one:

```json
{
  "cmd": "gmsg_ok",
  "room": "originChats",
  "listener": "optional-echo-tag"
}
```

### `pmsg`

Send a value privately to one member of a room this connection is in. `to` is the recipient's username or user ID; `id` is accepted in its place.

```json
{
  "cmd": "pmsg",
  "room": "originChats",
  "to": "rm",
  "val": { "type": "wave" },
  "listener": "optional-echo-tag"
}
```

Only the recipient's connections that are in that room receive the `pmsg`. You get `pmsg_ok`, with `listener` echoed back if you sent one:

```json
{
  "cmd": "pmsg_ok",
  "room": "originChats",
  "listener": "optional-echo-tag"
}
```

### `set_status`

Set your status text, your presence, or both. Send at least one of them.

```json
{
  "cmd": "set_status",
  "status": "working on rotur",
  "presence": "idle"
}
```

| Field | Description |
| --- | --- |
| `status` | Status text, up to 128 bytes. One status is shared by all your connections |
| `presence` | `online`, `idle`, `dnd`, or `invisible` (case-insensitive). With several connections, the most recently set presence wins |

`invisible` hides you from room member lists and stops your status updates from being sent. Users who aren't connected show as `offline` over HTTP.

There's no direct reply. If nothing changed, nothing is sent. Otherwise everyone sharing a room with you, and your own connections, receive a `status_update`. When you switch to `invisible`, room members receive `member_leave` instead; when you switch back, they receive `member_join`.

### `add_activity`

Add an activity (rich presence, such as what you're listening to), or replace one with the same `id`. Put the activity's fields at the top level next to `cmd`.

```json
{
  "cmd": "add_activity",
  "id": "spotify",
  "title": "Listening to Spotify",
  "application": { "name": "Spotify", "url": "https://spotify.com" },
  "image": "https://spotify.com/album-art.jpg",
  "url": "https://open.spotify.com/track/abc",
  "status": "Vibing",
  "start_time": 1715054000000,
  "media": {
    "title": "Bohemian Rhapsody",
    "artist": "Queen",
    "album": "A Night at the Opera",
    "start": 1715054321000,
    "end": 1715054600000
  }
}
```

Only `id` is required. The server also stores Discord-style fields (`name`, `type`, `details`, `state`, `timestamps`, `assets`, `party`, `buttons`, and others) and passes any unknown fields through unchanged.

Each connection can have up to 5 activities. Activities from all your connections are merged by `id`, and a connection's activities are removed when it closes. Room members, and your own connections, receive a `status_update` with your full activity list. Sending an activity identical to the current one does nothing.

### `remove_activity`

Remove one of your activities by `id`. Room members receive a `status_update`.

```json
{
  "cmd": "remove_activity",
  "id": "spotify"
}
```

### `gift_subscribe`

Get live updates for a [credit gift](../../claw/api-endpoints/gifts.md) by its `id`. You can subscribe to up to 20 gifts per connection.

```json
{
  "cmd": "gift_subscribe",
  "gift_id": "gift-id"
}
```

Reply:

```json
{
  "cmd": "gift_subscribed",
  "gift_id": "gift-id",
  "gift": { "...": "the gift" }
}
```

You then receive `gift_update` whenever the gift changes. The gift's creator and claimer get the full gift object; everyone else gets the public view.

### `gift_unsubscribe`

Stop updates for a gift.

```json
{
  "cmd": "gift_unsubscribe",
  "gift_id": "gift-id"
}
```

Reply:

```json
{ "cmd": "gift_unsubscribed", "gift_id": "gift-id" }
```

### `ping`

```json
{ "cmd": "ping" }
```

Accepted and ignored. It gets no reply and doesn't keep the connection open; see [Keepalive](#keepalive).

## Messages you receive

Besides the replies above, the server sends these.

### `room_state`

Sent after each `join_ok` and in reply to `room_state`. `members` lists everyone in the room whose presence isn't `invisible`, including you. `data` holds the member's profile overlay and represented group tag.

```json
{
  "cmd": "room_state",
  "room": "originChats",
  "members": [
    {
      "user_id": "abc123",
      "username": "mist",
      "status": "working on rotur",
      "presence": "online",
      "activities": [],
      "data": { "overlay": null, "group": "mygroup" }
    }
  ]
}
```

### `member_join`

A user joined a room you're in, or became visible again.

```json
{
  "cmd": "member_join",
  "room": "originChats",
  "user_id": "def456",
  "username": "rm",
  "status": "",
  "presence": "online",
  "activities": []
}
```

### `member_leave`

A user left a room you're in, closed their last connection in it, or switched to `invisible`.

```json
{
  "cmd": "member_leave",
  "room": "originChats",
  "user_id": "def456"
}
```

### `status_update`

A user who shares a room with you changed their status, presence, or activities. You also receive your own. A user's update is sent to each of your connections once, however many rooms you share.

```json
{
  "cmd": "status_update",
  "user_id": "abc123",
  "username": "mist",
  "status": "working on rotur",
  "presence": "idle",
  "activities": []
}
```

When the update is caused by one of the user's connections leaving a room while another stays, the message includes `room` and leaves out `activities`. When moderation clears a user's status, it includes `room` and leaves out `username`.

### `gmsg`

A room broadcast from another member. `timestamp` is in Unix milliseconds.

```json
{
  "cmd": "gmsg",
  "room": "originChats",
  "val": "hello everyone",
  "origin": { "user_id": "abc123", "username": "mist" },
  "timestamp": 1715054000000
}
```

### `pmsg`

A private message from another member of the room.

```json
{
  "cmd": "pmsg",
  "room": "originChats",
  "val": { "type": "wave" },
  "origin": { "user_id": "abc123", "username": "mist" },
  "timestamp": 1715054000000
}
```

### `profile_update`

A user who shares a room with you changed their profile. `key` is `pfp`, `sys.banner`, `sys.overlay`, `display_name`, `username`, or `sys.group`. For `sys.group`, `value` is the tag of the group they now represent.

```json
{
  "cmd": "profile_update",
  "user_id": "abc123",
  "username": "mist",
  "key": "display_name",
  "value": "Mist"
}
```

### `key_update`

A key on **your own** account changed, such as `sys.friends`, `sys.requests`, `sys.blocked`, or `sys.transactions`. Sent to all your connections.

```json
{
  "cmd": "key_update",
  "key": "sys.friends",
  "value": ["rm", "temp"]
}
```

### `gift_update`

A gift you subscribed to changed. `event` is `ready`, `claimed`, `cancelled`, or `expired`.

```json
{
  "cmd": "gift_update",
  "event": "claimed",
  "gift_id": "gift-id",
  "gift": { "...": "the gift" }
}
```

## Keepalive

The server sends a WebSocket ping frame every 30 seconds. After you authenticate, each pong frame you send back keeps the connection open for another 120 seconds; if 120 seconds pass without one, the server closes the connection. Browsers and most WebSocket libraries answer pings automatically.

{% hint style="warning" %}
On this socket only pong frames count. JSON messages, including `{"cmd": "ping"}`, don't extend the timeout, so a client whose pongs are dropped (for example by a proxy that filters control frames) is disconnected even while it's sending commands.
{% endhint %}

## Errors

Errors come back as an `error` message. The connection stays open.

```json
{
  "cmd": "error",
  "message": "not in room: originChats"
}
```

| Command | Messages |
| --- | --- |
| Any | `invalid json`, `missing cmd`, `invalid cmd`, `unknown command`, `not authenticated` |
| `auth` | `key required`, `invalid key`, `already authenticated` |
| `join` | `rooms required`, `invalid room name: <name>`, `already in room: <name>`, `room limit reached` |
| `leave` | `rooms required`, `not in room: <name>` |
| `room_state` | `room required`, `not in room: <room>` |
| `gmsg` | `invalid room`, `room required`, `not in room: <room>`, `val required` |
| `pmsg` | `invalid room`, `room required`, `not in room: <room>`, `val required`, `to required`, `user not in room` (the recipient doesn't exist or isn't in that room) |
| `set_status` | `Token lacks permission: account:profile`, `Custom status is blocked for this account` (with ` until <time>` if the block expires), `invalid status`, `status too long`, `invalid presence`, `invalid presence value`, `status or presence required` |
| `add_activity` | `Token lacks permission: account:profile`, `Custom status is blocked for this account`, `id required`, `invalid id`, `invalid activity`, `activity limit reached` |
| `remove_activity` | `Token lacks permission: account:profile`, `Custom status is blocked for this account`, `id required`, `activity not found` |
| `gift_subscribe` | `Token lacks permission: gifts:view`, `gift_id required`, `gift not found`, `already subscribed to gift`, `gift subscription limit reached` |
| `gift_unsubscribe` | `gift_id required`, `not subscribed to gift` |

## HTTP endpoints

Read and set status without a WebSocket connection.

### GET `/v2/status/live`

Get a user's current status. The legacy path is `GET /status/get`.

**Auth:** None.

#### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | query | string | Yes | The username to look up |

#### Example

```http
GET /v2/status/live?name=mist
```

**Response `200`:** if the user isn't connected, you get their saved status text with `presence` `offline` and an empty `activities` list.

```json
{
  "username": "mist",
  "status": "working on rotur",
  "presence": "online",
  "activities": [
    { "id": "spotify", "title": "Listening to Spotify" }
  ]
}
```

#### Errors

| Status | When |
| --- | --- |
| `400` | `name` is missing (`name parameter missing`) |
| `404` | No account has that username (`user not found`) |
| `404` | The user is connected with presence `invisible` (`no status`) |

### PUT `/v2/status/live`

Set your status text, presence, or both. The legacy path is `POST /status/set`. Room members on the WebSocket receive the same `status_update`, `member_join`, or `member_leave` messages as for `set_status`.

**Auth:** Required. Moderation must not have blocked custom status on your account.

#### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `status` | body | string | One of the two | Up to 128 bytes |
| `presence` | body | string | One of the two | `online`, `idle`, `dnd`, or `invisible` (case-insensitive) |

#### Example

```http
PUT /v2/status/live
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "status": "working on rotur", "presence": "dnd" }
```

**Response `200`:**

```json
{
  "ok": true
}
```

#### Errors

| Status | When |
| --- | --- |
| `400` | The body isn't valid JSON (`Invalid request body`) |
| `400` | Neither field is sent (`status or presence required`) |
| `400` | `status` is over 128 bytes (`status too long`) |
| `400` | `presence` isn't an allowed value (`invalid presence value`) |
| `403` | Moderation has blocked custom status on your account (`Custom status is blocked for this account`) |
