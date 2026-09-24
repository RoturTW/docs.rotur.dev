# Packets

Packets are messages that projects send to each other through Rotur. A packet goes to a user on a **port**, and the receiving project reads it from that port's queue. Ports are rooms on the Rotur socket, so both users have to be connected and logged in.

## Send a message

```
send message [Hello] to user [targetUser] on port: [port] from port: [port]
```

Command. Joins the `TARGET` port and sends the payload to `USER` there. If `USER` is empty, the message goes to everyone on that port.

| Argument | Default | Description |
| --- | --- | --- |
| `PAYLOAD` | `Hello` | The data to send |
| `USER` | `targetUser` | Username (or user ID) of the recipient. Leave it empty to send to everyone on the port. |
| `TARGET` | `port` | The port to send on. The recipient reads the packet from this port. If empty, your designation is used. |
| `SOURCE` | `port` | Your port, so the recipient knows where to reply |

{% hint style="warning" %}
The recipient only gets the packet if they are on the same port. A client joins its designation when it connects, and joins a port the first time it sends on it. The simplest setup is for both sides to send on the same port, or to use the designation as the port.
{% endhint %}

## Read packets

Received packets are queued per port.

| Block | Type | Returns |
| --- | --- | --- |
| `when message received` | Hat | Fires when a packet arrives |
| `(get packets from port [TARGET])` | Reporter | JSON array of the packets on the port |
| `(first packet on port [TARGET])` | Reporter | The oldest packet on the port, as JSON |
| `([DATA] of first packet on port [TARGET])` | Reporter | One field of the oldest packet (see below) |
| `(number of packets on port [TARGET])` | Reporter | How many packets are queued on the port |
| `(all open targets)` | Reporter | JSON array of ports that have received packets |
| `(all packets)` | Reporter | JSON object of every port and its packets |
| `(pop first of port [TARGET])` | Reporter | Removes the oldest packet from the port and returns it as JSON |
| `delete all packets on port [TARGET]` | Command | Clears one port |
| `delete all packets` | Command | Clears every port |

The `[DATA]` dropdown has these options:

| Option | Value |
| --- | --- |
| `origin` | Username of the sender |
| `client` | The sender's `{"system": ..., "version": ...}` object, as JSON |
| `source port` | The port the sender set as `SOURCE` |
| `payload` | The data that was sent |
| `timestamp` | When Rotur relayed the packet, in milliseconds |

### Example

```
when message received
if <(number of packets on port [chat]) > [0]> then
  say (join ([origin] of first packet on port [chat]) (join [: ] ([payload] of first packet on port [chat])))
  set [packet v] to (pop first of port [chat])
end
```

## Packet format

A packet in a port queue looks like this:

```json
{
  "origin": "(username of the sender)",
  "client": {
    "system": "(system the sender connected with)",
    "version": "(version the sender connected with)"
  },
  "source": "(the sender's source port)",
  "payload": "(the data that was sent)",
  "timestamp": 1723684583612
}
```

## Raw packet queue

Every received packet is also added to one raw queue across all ports. Raw packets have the same fields plus `room`, the port the packet arrived on.

| Block | Type | Description |
| --- | --- | --- |
| `(get all raw packets)` | Reporter | JSON array of every received packet |
| `(first raw packet)` | Reporter | The oldest raw packet, as JSON |
| `pop first raw packet` | Command | Removes the oldest raw packet |
| `delete all raw packets` | Command | Clears the raw queue |

## Synced variables

Synced variables are key/value pairs shared with one other user on your designation. Setting one sends the value to that user, who can then read it under your username.

| Block | Type | Description |
| --- | --- | --- |
| `sync variable with [user] of [key] to [value]` | Command | Sets a variable and sends it to `USER` |
| `(get synced variable with [user] of [key])` | Reporter | The value as JSON |
| `delete synced variable with [user] of [key]` | Command | Deletes a variable on both sides |
| `(get synced variables with [user])` | Reporter | JSON object of every variable shared with `USER` |
