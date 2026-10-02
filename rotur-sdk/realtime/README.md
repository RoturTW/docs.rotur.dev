# Realtime

Live presence over WebSocket, web push notifications, and Beam transfers between devices.

| Page | Covers |
| --- | --- |
| [Status and WebSocket](status.md) | `rotur.status` and `rotur.socket`: presence, rooms, activities, messages |
| [Push notifications](push.md) | `rotur.push`: register devices and send push notifications |
| [Beam](#beam) | `rotur.beam`: send text and files to the user's other devices and to people nearby |

## Beam

`rotur.beam` connects to Beam (`beam.rotur.dev`) to send text and files between devices. Transfers go peer to peer over WebRTC where possible, and fall back to an encrypted relay through the Beam server. Beam does not connect until you call `connect()`.

**Auth:** Required. Beam signs in with a validator for the key `rotur-beam`, so sub-tokens need `validators:generate`. Without a token, Beam emits an `error` event and stays disconnected.

```ts
rotur.beam.on("peers", (peers) => console.log(peers.map((p) => p.username)));
rotur.beam.on("incoming", (request) => rotur.beam.accept(request.id));
rotur.beam.on("file", ({ file, fromName }) => console.log(fromName, "sent", file.name));
rotur.beam.on("text", ({ text, fromName }) => console.log(fromName, "says", text));

rotur.beam.connect();

// later
const transferId = rotur.beam.sendFiles(peerId, fileInput.files!);
rotur.beam.sendTextToOwnDevices("Hello from my laptop");
```

| Member | Description |
| --- | --- |
| `connect()` | Opens the connection. It reconnects on its own until you call `disconnect()` |
| `disconnect()` | Closes the connection and cancels every transfer. `rotur.logout()` calls it for you |
| `reconnect()` | Reopens a dropped connection straight away |
| `status` | `"disconnected"`, `"connecting"`, or `"connected"` |
| `peers` | Devices you can see: your own (`same_account: true`) and others on your network or in your room |
| `ownDevices` | `peers` filtered to your own devices |
| `on(event, handler)` | Listens for an event, below. Returns a function that removes the handler |
| `sendText(peerId, text)` | Sends text to one device |
| `sendTextToOwnDevices(text)` | Sends text to all your other devices. Returns how many it sent to |
| `sendFiles(peerId, files)` | Offers files to a device. Returns the transfer ID, or `null` when `files` is empty. Fails if the other device does not answer within 60 seconds |
| `accept(transferId, writable?)` | Accepts an incoming transfer. Pass a `writable` (`{ write, close, abort? }`) to stream the data instead of getting a `File` at the end |
| `decline(transferId)` | Declines an incoming transfer |
| `cancel(transferId)` | Cancels a transfer in either direction |
| `joinRoom(room)` / `leaveRoom()` | Joins or leaves a named room, so people outside your network can see each other. Room names are lowercased |
| `createShare(file)` | Creates a share link for one file. Resolves to a URL such as `https://beam.rotur.dev/#beam=<id>`. The file is sent to whoever opens it while you stay connected |
| `joinShare(shareId)` | Opens a share link by its ID, so the file is offered to you |
| `loadIceConfiguration()` | Refreshes the STUN and TURN servers from Beam. `connect()` calls it for you |

| Event | Value |
| --- | --- |
| `status` | The new connection status |
| `peers` | The current list of peers |
| `incoming` | `{ id, fromPeerId, fromName, files, totalBytes }`: someone wants to send you files |
| `transfer` | A transfer changed: `{ id, direction, peerId, peerName, files, status, transferredBytes, totalBytes, currentFile, startedAt?, error? }` |
| `file` | `{ transferId, fromPeerId, fromName, file, index, total }`: a file finished arriving |
| `text` | `{ fromPeerId, fromName, text }` |
| `room` | The room you are now in, or `""` after leaving |
| `share` | `{ status: "ready" \| "joined" \| "error", peer?, message? }` |
| `error` | An `Error` |

Transfer `status` is `"requested"`, `"connecting"`, `"transferring"`, `"done"`, `"declined"`, `"cancelled"`, or `"error"`.

To use Beam without a `Rotur` client, import `BeamClient` from `rotur-sdk` and pass `{ token }`, where `token` is a validator for `rotur-beam` or a function that returns one. It also takes `serverUrl`, `userAgent`, `autoAccept`, `rtcConfiguration`, `requestTimeoutMs`, and `connectTimeoutMs`.
