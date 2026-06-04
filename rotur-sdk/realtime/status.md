# Status & WebSocket

The SDK provides both HTTP status lookups and a full real-time WebSocket client.

## HTTP Status Lookup

```ts
const status = await rotur.status.get("alice");
// { username, status, presence, activities: { ... } }
```

## WebSocket Connection

The real-time WebSocket gives you live presence, room membership, and activity updates.

### Connect

```ts
const { user_id, username, user } = await rotur.connectSocket();
```

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

### Rooms

```ts
rotur.socket.join("lobby");
rotur.socket.join(["lobby", "dev"]);

rotur.socket.leave("lobby");

const rooms = await rotur.socket.listRooms();
rotur.socket.roomState("lobby");
```

### Presence & Status

```ts
rotur.socket.setPresence("idle");    // "online" | "idle" | "dnd" | "invisible"
rotur.socket.setStatus("Working on stuff");
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

// Clear an activity
rotur.socket.clearActivity("My Game");
```

### WebSocket Events

| Event | Description |
|-------|-------------|
| `ready` | Authenticated and connected |
| `join_ok` | Successfully joined a room |
| `room_state` | Full state of a room (members list) |
| `member_join` | A user joined a room |
| `member_leave` | A user left a room |
| `status_update` | A user changed presence/status/activity |
| `profile_update` | A user's profile key changed |
| `key_update` | A system key changed |
| `error` | WebSocket error |
| `leave_ok` | Successfully left a room |
| `rooms` | Response to `listRooms()` |

### Disconnect

```ts
rotur.socket.disconnect();
```

The socket auto-reconnects on disconnect (with a 3s delay) unless you explicitly call `disconnect()`.
