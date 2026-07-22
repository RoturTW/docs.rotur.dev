# Global Message (gmsg)

## Global Messaging (`gmsg`)

The `gmsg` command allows a client to send a broadcast message to all users in every room that the client is currently a member of.

### Client Request

To send a global message, the client should format the JSON message as follows:

```json
{
  "cmd": "gmsg",
  "val": "Hello World"
}
```

**Field Descriptions:**

* **`cmd`**: Must be `"gmsg"`. This tells the server the type of message.
* **`val`**: A string containing the message content that you wish to broadcast.

### Server Response

After processing the global message, the server responds with a status confirmation:

```json
{
  "cmd": "statuscode",
  "code": "I:100 | OK",
  "code_id": 100
}
```

In addition, the server broadcasts the message to all users in the rooms that the sender is a member of. The broadcasted message will have the following format:

```json
{
  "cmd": "gmsg",
  "val": "Hello World",
  "origin": { "username": "sender_username" },
  "rooms": "room_name"
}
```

**Notes:**

* The **`origin`** field indicates the username of the sender.
* The **`rooms`** field specifies which room the message is being sent to. If the sender is in multiple rooms, each room will receive its own broadcasted message.
