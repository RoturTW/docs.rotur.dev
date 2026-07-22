# Status & WebSocket

The SDK provides both HTTP status lookups and a full real-time WebSocket client.

## HTTP Status Lookup

```ts
const status = await rotur.status.get("alice");
// { username, status, presence, activities: { ... } }
```

No auth required.

## WebSocket Connection

The real-time WebSocket gives you live presence, room membership, and activity updates.

### Connect

```ts
const { user_id, username, user } = await rotur.connectSocket();
```

If you create the client with a token (or call `login()`), the socket connects automatically in the background. Call `connectSocket()` when you want to await the connection or read the ready payload.

### Listen for Events

```ts
// Listen for a specific command
const unsub = rotur.socket.on("status_update", (msg) => {
  console.log(msg.user_id, "changed to", msg.presence);
});

// Stop listening
unsub();

// Listen for all events
rotur.socket.on("*", (msg) => {
  console.log(msg.cmd, msg);
});

// Wait for one event
const msg = await rotur.socket.once("member_join");
```

### Connection State

```ts
rotur.socket.connected;    // boolean
rotur.socket.userId;       // your user id (after ready)
rotur.socket.username;     // your username (after ready)
rotur.socket.joinedRooms;  // rooms you are currently in
```

### Rooms

```ts
rotur.socket.join("lobby");
rotur.socket.join(["lobby", "dev"]);

rotur.socket.leave("lobby");

const rooms = await rotur.socket.listRooms();
rotur.socket.roomState("lobby");  // server replies with a "room_state" event
```

### Presence & Status

```ts
// Set your status text, and optionally your presence
rotur.socket.setStatus("Working on stuff");
rotur.socket.setStatus("Working on stuff", "idle");
// presence: "online" | "idle" | "dnd" | "invisible"
```

### Activities (Rich Presence)

```ts
// Set a "playing" activity
rotur.socket.setPlaying("My Game", {
  title: "Playing My Game",
  status: "In a match",
  image: "https://example.com/icon.png",
  url: "https://mygame.com",
});

// Set a "listening" activity
rotur.socket.setMusic("Spotify", {
  title: "Song Name",
  artist: "Artist Name",
  album: "Album Name",
  start: Date.now(),
  end: Date.now() + 180000,
});

// Full control
rotur.socket.addActivity({
  id: "my-activity",
  title: "Doing something",
  status: "In progress",
});

// Clear an activity by id
rotur.socket.clearActivity("My Game");   // alias of removeActivity
rotur.socket.removeActivity("my-activity");
```

### Live Account Keys

The `ready` payload includes your account keys, and the socket keeps them cached and updated. Read them via `rotur.me`:

```ts
rotur.me.getKey("bio");
rotur.me.getAllKeys();
rotur.me.onKeyChange((key, value, oldValue) => { /* ... */ });
```

### WebSocket Events

| Event | Description |
|-------|-------------|
| `ready` | Authenticated and connected |
| `join_ok` | Successfully joined a room |
| `leave_ok` | Successfully left a room |
| `room_state` | Full state of a room (members list) |
| `member_join` | A user joined a room |
| `member_leave` | A user left a room |
| `status_update` | A user changed presence/status/activity |
| `profile_update` | A user's profile key changed |
| `key_update` | One of your account keys changed |
| `rooms` | Response to `listRooms()` |
| `error` | WebSocket error |
| `close` | Connection closed |

### Disconnect

```ts
rotur.socket.disconnect();
```

The socket auto-reconnects on disconnect (with a 3s delay) unless you explicitly call `disconnect()`.
