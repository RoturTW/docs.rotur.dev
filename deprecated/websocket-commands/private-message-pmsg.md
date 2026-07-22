# Private Message (pmsg)

## Private Messaging (`pmsg`)

The `pmsg` command is used to send a private message to a specific user.

### Client Request

To send a private message, the client should format the JSON message like this:

```json
{
  "cmd": "pmsg",
  "id": "recipient_username",
  "val": "Hello"
}
```

**Field Descriptions:**

* **`cmd`**: Must be `"pmsg"`. This indicates a private message.
* **`id`**: The username of the intended recipient.
* **`val`**: A string containing the content of the private message.

### Server Response

Once the server processes the private message, it sends a status confirmation back to the sender:

```json
{
  "cmd": "statuscode",
  "code": "I:100 | OK",
  "code_id": 100
}
```

The server also forwards the private message to the recipient with the following format:

```json
{
  "cmd": "pmsg",
  "val": "Hello",
  "origin": { "username": "sender_username" },
  "rooms": "room_name"
}
```

**Notes:**

* The **`origin`** field shows the sender's username.
* The **`rooms`** field indicates the context or room in which the private message was sent. This is important if the recipient shares multiple rooms with the sender.
* The private message is delivered only if the recipient is connected and present in at least one room shared with the sender.
