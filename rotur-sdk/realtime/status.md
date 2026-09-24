# Status and WebSocket

Read and set user status over HTTP with `rotur.status`, or use the real-time WebSocket, `rotur.socket`, for live presence, rooms, activities, and messages.

## rotur.status.get(username)

Gets a user's current status.

**Auth:** None.

```ts
const status = await rotur.status.get("alice");
```

**Returns:** `{ username, status, presence, activities }`. `activities` is an object keyed by activity ID.

## rotur.status.setLive(options)

Sets your status text and presence over HTTP.

**Auth:** Required. No specific permission.

| Option | Type | Description |
| --- | --- | --- |
| `status` | string | Status text |
| `presence` | string | `"online"`, `"idle"`, `"dnd"`, or `"invisible"` |

```ts
await rotur.status.setLive({ status: "Working on stuff", presence: "idle" });
```

**Returns:** `{ ok: true }`

## WebSocket

The socket connects to `wss://api.rotur.dev/status/ws` and authenticates with your token. It opens in the background when the client gets a token (from the constructor, `setToken()`, or `login()`). The server sends a WebSocket ping every 30 seconds and the browser answers it for you, which keeps the connection open. The SDK also sends a `ping` command every 25 seconds; the server accepts it, but it does not extend the timeout. It reconnects 3 seconds after the connection drops unless you called `disconnect()`.

{% hint style="warning" %}
Methods that send (`join`, `setStatus`, `sendGroupMessage`, and so on) drop the message without an error when the socket is not open. Await `rotur.connectSocket()` first.
{% endhint %}

## rotur.connectSocket()

Waits for the socket to connect and authenticate.

**Auth:** Required. Throws `ApiError` `401` when no token is set.

```ts
const { user_id, username, user } = await rotur.connectSocket();
```

**Returns:** `{ user_id, username, user }`. `user` holds your account keys, which are cached for [`rotur.me.getKey()`](../account/me.md).

## Connection state

| Property | Type | Description |
| --- | --- | --- |
| `rotur.socket.connected` | boolean | Whether the socket is authenticated |
| `rotur.socket.userId` | string \| null | Your user ID, after `ready` |
| `rotur.socket.username` | string \| null | Your username, after `ready` |
| `rotur.socket.joinedRooms` | string[] | Rooms you are in |

## rotur.socket.on(cmd, handler)

Calls `handler` for every message with the given `cmd`. See [Events](#events).

```ts
const unsubscribe = rotur.socket.on("status_update", (msg) => {
  if (msg.cmd === "status_update") console.log(msg.user_id, msg.presence);
});

unsubscribe();
```

**Returns:** a function that removes the handler. `rotur.socket.off(cmd, handler)` does the same.

`on()` does not accept `"*"`. To receive every message, listen for the `"*"` DOM event instead. `RoturSocket` is an `EventTarget`, and each message is in `event.detail`:

```ts
rotur.socket.addEventListener("*", (event) => {
  const msg = (event as CustomEvent).detail;
  console.log(msg.cmd, msg);
});
```

## rotur.socket.once(cmd)

Waits for the next message with the given `cmd`.

```ts
const msg = await rotur.socket.once("member_join");
```

**Returns:** `Promise<WSMessage>`

## rotur.socket.join(rooms)

Joins one room or several. The server replies with `join_ok` for each room.

```ts
rotur.socket.join("lobby");
rotur.socket.join(["lobby", "dev"]);
```

## rotur.socket.leave(rooms)

Leaves one room or several. The server replies with `leave_ok`.

```ts
rotur.socket.leave("lobby");
```

## rotur.socket.listRooms()

Lists the rooms you are in, as the server sees them.

```ts
const rooms = await rotur.socket.listRooms();
```

**Returns:** `Promise<string[]>`

## rotur.socket.roomState(room)

Asks for a room's member list. The server replies with a `room_state` message.

```ts
rotur.socket.roomState("lobby");
const state = await rotur.socket.once("room_state");
```

## rotur.socket.sendGroupMessage(room, val, listener?)

Sends `val` to everyone in a room. Members receive a `gmsg` message, and you receive `gmsg_ok`.

```ts
rotur.socket.sendGroupMessage("lobby", { text: "hello" });
```

## rotur.socket.sendPrivateMessage(room, to, val, listener?)

Sends `val` to one user in a room. `to` is a username or user ID. The recipient receives a `pmsg` message, and you receive `pmsg_ok`.

```ts
rotur.socket.sendPrivateMessage("lobby", "alice", { text: "hi" });
```

## rotur.socket.setStatus(status, presence?)

Sets your status text and, optionally, your presence.

```ts
rotur.socket.setStatus("Working on stuff");
rotur.socket.setStatus("Working on stuff", "idle");
// presence: "online" | "idle" | "dnd" | "invisible"
```

## rotur.socket.addActivity(activity)

Adds an activity (rich presence). `activity` needs an `id` and a `title`; see the `Activity` type for the other fields.

```ts
rotur.socket.addActivity({
  id: "my-activity",
  title: "Doing something",
  status: "In progress",
});
```

## rotur.socket.setPlaying(application, options?)

Sets a "playing" activity. The activity ID is `application`.

| Option | Type | Description |
| --- | --- | --- |
| `title` | string | Title. Default: `application` |
| `status` | string | Status text |
| `image` | string | Image URL |
| `url` | string | Application URL |

```ts
rotur.socket.setPlaying("My Game", {
  title: "Playing My Game",
  status: "In a match",
  image: "https://example.com/icon.png",
  url: "https://mygame.com",
});
```

## rotur.socket.setMusic(application, media, title?)

Sets a "listening" activity. The activity ID is `application`, and `title` defaults to `Listening to <application>`.

```ts
rotur.socket.setMusic("Spotify", {
  title: "Song Name",
  artist: "Artist Name",
  album: "Album Name",
  start: Date.now(),
  end: Date.now() + 180000,
});
```

`media` is `{ title, start, end, artist?, album? }`.

## rotur.socket.removeActivity(id)

Removes an activity. `clearActivity(id)` is an alias.

```ts
rotur.socket.removeActivity("my-activity");
rotur.socket.clearActivity("My Game");
```

## rotur.socket.disconnect()

Closes the socket, stops reconnecting, and clears the joined rooms and cached account keys.

```ts
rotur.socket.disconnect();
```

## Events

Messages you can listen for with `on()`, `once()`, or `addEventListener()`:

| `cmd` | When |
| --- | --- |
| `ready` | Authenticated. Has `user_id`, `username`, and `user` |
| `join_ok` | You joined a room |
| `leave_ok` | You left a room |
| `room_state` | Reply to `roomState()`: `room` and `members` |
| `member_join` | A user joined a room you are in |
| `member_leave` | A user left a room you are in |
| `status_update` | A user changed status, presence, or activities |
| `profile_update` | A user changed a profile key |
| `key_update` | One of your account keys changed |
| `key_delete` | One of your account keys was deleted |
| `gmsg` | A room message from `sendGroupMessage()` |
| `pmsg` | A private message from `sendPrivateMessage()` |
| `gmsg_ok` | Your room message was sent |
| `pmsg_ok` | Your private message was sent |
| `rooms` | Reply to `listRooms()` |
| `error` | The server sent an error, or the connection failed |
| `close` | The connection closed |
