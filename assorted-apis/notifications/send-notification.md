# Send a Notification

### POST `/notify/:username`

Sends a push notification to the target user's registered endpoints for the specified source. The sender must be in the target's allowed list for that source, and must not be blocked by the target.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `username` | The Rotur username to notify |

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `source` | string | Yes | The source the notification is from (must match an allowed entry) |
| `title` | string | No | Notification title (max 256 chars) |
| `body` | string | No | Notification body text (max 1024 chars) |
| `data` | object | No | Arbitrary JSON data to include in the push payload |

**Request:**

```json
{
  "source": "originChats",
  "title": "New message",
  "body": "Hey, are you online?",
  "data": {
    "chat_room": "general",
    "message_id": "abc123"
  }
}
```

**Response (200):**

```json
{
  "message": "notification sent",
  "endpoints_hit": 2,
  "source": "originChats"
}
```

**Push Payload Delivered to Endpoints:**

Each registered endpoint for the source receives a POST request with:

```json
{
  "type": "notification",
  "source": "originChats",
  "from": "mist",
  "title": "New message",
  "body": "Hey, are you online?",
  "data": {
    "chat_room": "general",
    "message_id": "abc123"
  },
  "sent_at": 1715054321000
}
```

Push delivery is fire-and-forget — the response is returned immediately after dispatching to all matching endpoints.

**Error Responses:**

| Status | Condition |
| --- | --- |
| 403 | Sender is not in the target's allowed list for this source |
| 403 | Target has blocked the sender |
| 404 | Target user not found |
| 404 | Target has no registered endpoints for this source |
