# Notification Log

### GET `/notify/log`

Returns the last 200 notifications you received.

**Example:**

```
GET /notify/log
```

**Response (200):**

```json
{
  "log": [
    {
      "from": "mist",
      "source": "originChats",
      "title": "New message",
      "body": "Hey, are you online",
      "at": 1715054321000
    }
  ],
  "count": 1
}
```

`title` and `body` are omitted from an entry when they were empty. `at` is a Unix timestamp in milliseconds.
