# Status WebSocket

The status websocket provides real-time presence and activity information for Rotur users. It operates as a room-based system where clients join rooms to see each other's status, and receive live updates when anyone in the room changes their status, presence, or activity.

## Connecting

Connect to the websocket endpoint:

```
wss://api.rotur.dev/status/ws
```

The connection supports JSON-based commands. You must authenticate before using any other commands.

## Authentication

### `auth`

Authenticate the connection using your Rotur user token.

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
  "cmd": "auth_ok",
  "user_id": "abc123",
  "username": "mist"
}
```

**Error:**

```json
{
  "cmd": "error",
  "message": "invalid key"
}
```

## Rooms

Rooms are the core organisational unit. You only see status updates from users who are in the same rooms as you. Room names must match `^[a-zA-Z0-9_\-:]+$` and be at most 64 characters. Each connection can join up to 10 rooms.

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

**Response:**

```json
{
  "cmd": "join_ok",
  "room": "originChats"
}
```

Upon joining, you also receive the current state of the room:

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

Other members in the room receive a `member_join` event:

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

If you have multiple connections for the same user, leaving one connection will downgrade to a `status_update` rather than `member_leave` if your other connections are still in the room.

### `rooms`

List the rooms you are currently in.

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

**Response:**

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

## Presence

Presence determines your visibility to other users in rooms. A user with multiple connections uses the most visible presence across all of them.

| Presence | Behaviour |
| --- | --- |
| `online` | Fully visible, appears in room member lists |
| `idle` | Visible, indicates away/afk |
| `dnd` | Visible, indicates do not disturb |
| `invisible` | Hidden from room member lists entirely |

### `set_presence`

**Send:**

```json
{
  "cmd": "set_presence",
  "presence": "idle"
}
```

When switching from visible to invisible, other room members receive a `member_leave`. When switching from invisible to visible, they receive a `member_join`. Otherwise, a `status_update` is broadcast.

## Status

A simple text string (max 128 characters) shown alongside your presence.

### `set_status`

**Send:**

```json
{
  "cmd": "set_status",
  "status": "working on rotur"
}
```

Other room members receive a `status_update` event:

```json
{
  "cmd": "status_update",
  "user_id": "abc123",
  "username": "mist",
  "status": "working on rotur",
  "presence": "online",
  "activities": []
}
```

## Activities

Activities are rich presence indicators — what you're listening to, what app you're using, etc. Each connection can have up to 5 activities, each identified by a unique `id`.

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

**Send:**

```json
{
  "cmd": "add_activity",
  "id": "spotify",
  "title": "Listening to Spotify",
  "application": {
    "name": "Spotify",
    "url": "https://spotify.com"
  },
  "media": {
    "title": "Bohemian Rhapsody",
    "artist": "Queen",
    "album": "A Night at the Opera",
    "start": 1715054321000,
    "end": 1715054600000
  }
}
```

Room members receive a `status_update` with the new activity included.

### `remove_activity`

**Send:**

```json
{
  "cmd": "remove_activity",
  "id": "spotify"
}
```

Room members receive a `status_update` reflecting the removal.

## Multiple Connections

A single user can have multiple websocket connections simultaneously (e.g. on different devices). The system merges their state:

- **Presence**: The most visible presence wins (online > idle > dnd > invisible)
- **Status**: The most recently set status is used
- **Activities**: Activities from all connections are merged by `id`

When all connections for a user leave a room, the user is fully removed and a `member_leave` is broadcast.

## HTTP Fallback

### GET `/status/get`

Retrieve the real-time status of a user without a websocket connection.

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
      "title": "Listening to Spotify",
      "media": {
        "title": "Bohemian Rhapsody",
        "artist": "Queen"
      }
    }
  ]
}
```

**Response (404):**

```json
{
  "error": "no status"
}
```

This only returns data for users with visible presence who are currently connected to the status websocket.

## Keepalive

The server sends ping frames every 30 seconds. The client must respond with pong frames. If no pong is received within 120 seconds, the connection is closed.
