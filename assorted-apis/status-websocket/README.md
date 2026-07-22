# Status WebSocket

The status websocket provides real-time presence and activity information for Rotur users. It is room-based: you join rooms to see other members' status, and you receive live updates when anyone in a shared room changes their status, presence, or activities.

## Connecting

Connect to the websocket endpoint:

```
wss://api.rotur.dev/status/ws
```

All messages are JSON objects with a `cmd` field. You must authenticate before using any other command. Errors always come back as:

```json
{
  "cmd": "error",
  "message": "some error message"
}
```

## Authentication

### `auth`

Authenticate the connection with your Rotur auth key. Both main account keys and sub-tokens work.

**Send:**

```json
{
  "cmd": "auth",
  "key": "your_rotur_auth_key"
}
```

**Response:**

```json
{
  "cmd": "ready",
  "user_id": "abc123",
  "username": "mist",
  "user": { "...": "your full user object, including sys.status" }
}
```

**Errors:** `key required`, `invalid key`, `already authenticated`. Any other command before authenticating returns `not authenticated`.

{% hint style="info" %}
If you authenticate with a sub-token, it needs the `account:profile` permission to use `set_status`, `add_activity`, and `remove_activity`. Joining rooms and reading state work with any valid token.
{% endhint %}

Your status and presence persist across sessions. When your last connection closes they are saved to your account, and restored the next time you authenticate.

## Rooms

Rooms are the core organisational unit. You only see status updates from users who share a room with you. Room names must match `^[a-zA-Z0-9_\-:.]+$` and be at most 64 characters. Each connection can join up to 200 rooms.

### `join`

Join one or more rooms.

**Send (single room):**

```json
{
  "cmd": "join",
  "rooms": "originChats"
}
```

**Send (multiple rooms):**

```json
{
  "cmd": "join",
  "rooms": ["originChats", "originChats:general"]
}
```

**Response (one per room):**

```json
{
  "cmd": "join_ok",
  "room": "originChats"
}
```

After each `join_ok` you also receive the current state of the room:

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
      "activities": []
    }
  ]
}
```

Other members in the room receive a `member_join` event (unless your presence is `invisible`):

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

**Errors:** `rooms required`, `invalid room name: <name>`, `already in room: <name>`, `room limit reached`.

### `leave`

Leave one or more rooms. Accepts the same `rooms` format as `join`.

**Send:**

```json
{
  "cmd": "leave",
  "rooms": "originChats"
}
```

**Response:**

```json
{
  "cmd": "leave_ok",
  "room": "originChats"
}
```

Other members receive a `member_leave` event:

```json
{
  "cmd": "member_leave",
  "room": "originChats",
  "user_id": "def456"
}
```

If you have multiple connections and another one is still in the room, other members receive a `status_update` instead of a `member_leave`.

**Errors:** `rooms required`, `not in room: <name>`.

### `rooms`

List the rooms this connection is in.

**Send:**

```json
{
  "cmd": "rooms"
}
```

**Response:**

```json
{
  "cmd": "rooms",
  "rooms": ["originChats", "originChats:general"]
}
```

### `room_state`

Request the current state of a room you are in.

**Send:**

```json
{
  "cmd": "room_state",
  "room": "originChats"
}
```

**Response:** the same `room_state` message shown under `join`. Members with `invisible` presence are excluded.

**Errors:** `room required`, `not in room: <room>`.

## Status and Presence

Your **status** is a text string (max 128 characters) shown alongside your **presence**. Presence determines your visibility in rooms:

| Presence | Behaviour |
| --- | --- |
| `online` | Fully visible, appears in room member lists |
| `idle` | Visible, indicates away/afk |
| `dnd` | Visible, indicates do not disturb |
| `invisible` | Hidden from room member lists entirely |

### `set_status`

Sets your status text, your presence, or both. At least one of `status` and `presence` is required.

**Send:**

```json
{
  "cmd": "set_status",
  "status": "working on rotur",
  "presence": "idle"
}
```

There is no direct reply on success. Users who share a room with you receive a `status_update` event:

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

When you switch from visible to `invisible`, room members receive a `member_leave` instead. When you switch back to a visible presence, they receive a `member_join`.

**Errors:** `status or presence required`, `status too long`, `invalid presence value`, `Token lacks permission: account:profile`.

## Activities

Activities are rich presence entries: what you are listening to, what app you are using, and so on. Each connection can have up to 5 activities, each identified by a unique `id`. Activities are removed automatically when the connection that added them closes.

### Activity Structure

```json
{
  "id": "spotify",
  "title": "Listening to Spotify",
  "application": {
    "name": "Spotify",
    "url": "https://spotify.com"
  },
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

All fields except `id` are optional.

### `add_activity`

Adds or replaces an activity. Send the activity fields at the top level alongside the `cmd`:

```json
{
  "cmd": "add_activity",
  "id": "spotify",
  "title": "Listening to Spotify",
  "media": {
    "title": "Bohemian Rhapsody",
    "artist": "Queen",
    "album": "A Night at the Opera",
    "start": 1715054321000,
    "end": 1715054600000
  }
}
```

Room members receive a `status_update` with your full activity list included. Sending an identical activity again is a no-op.

**Errors:** `id required`, `invalid id`, `activity limit reached`, `Token lacks permission: account:profile`.

### `remove_activity`

**Send:**

```json
{
  "cmd": "remove_activity",
  "id": "spotify"
}
```

Room members receive a `status_update` reflecting the removal.

**Errors:** `id required`, `activity not found`, `Token lacks permission: account:profile`.

## Other Server Events

### `profile_update`

Sent to everyone who shares a room with a user when their profile changes. `key` is one of `pfp`, `sys.banner`, `sys.overlay`, `display_name`, or `username`.

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

Sent to **your own** connections when a key on your account changes (for example `sys.friends`, `sys.requests`, `sys.blocked`, or `sys.transactions`):

```json
{
  "cmd": "key_update",
  "key": "sys.friends",
  "value": ["rm", "temp"]
}
```

## Multiple Connections

A single user can hold several websocket connections at once (for example on different devices). Their state is merged:

- **Presence**: the most recently set presence across all connections wins
- **Status**: one status is shared per user; the last `set_status` wins
- **Activities**: activities from all connections are merged by `id`

When the last of a user's connections leaves a room, the user is removed from it and a `member_leave` is broadcast.

## HTTP Endpoints

You can read and write status without a websocket connection.

### GET `/status/get`

{% hint style="info" %}
On v2 this is `GET /v2/status/live`.
{% endhint %}

**Query Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| `name` | Yes | The Rotur username to query |

**Response (200):**

```json
{
  "username": "mist",
  "status": "working on rotur",
  "presence": "online",
  "activities": [
    {
      "id": "spotify",
      "title": "Listening to Spotify"
    }
  ]
}
```

If the user is offline, their last persisted status and presence are returned with an empty `activities` list. If the user is connected but `invisible`, or has no stored status at all, you get a `404` with `{"error": "no status"}`. An unknown username returns a `404` with `{"error": "user not found"}`.

### POST `/status/set`

Sets your status text, presence, or both. Requires authentication (`auth` query parameter or `Authorization: Bearer` header).

{% hint style="info" %}
On v2 this is `PUT /v2/status/live`.
{% endhint %}

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `status` | string | No* | Status text (max 128 chars) |
| `presence` | string | No* | One of `online`, `idle`, `dnd`, `invisible` |

*At least one of the two is required.

**Request:**

```json
{
  "status": "working on rotur",
  "presence": "dnd"
}
```

**Response (200):**

```json
{
  "ok": true
}
```

Room members on the websocket receive the same `status_update` / `member_join` / `member_leave` events as if you had used `set_status`.

**Errors:** `400` for `status or presence required`, `status too long`, or `invalid presence value`.

## Keepalive

The server sends websocket ping frames every 30 seconds, and the client must reply with pong frames. If nothing is received for 120 seconds, the connection is closed. You can also send `{"cmd": "ping"}` yourself; the server ignores it, but it keeps the connection active.
