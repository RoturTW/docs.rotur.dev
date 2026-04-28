# Notification Log

### GET `/notify/log`

Returns the last 200 notifications received by the authenticated user.

**Response (200):**

```json
{
  "log": [
    {
      "from": "mist",
      "source": "originChats",
      "title": "New message",
      "body": "Hey, are you online?",
      "at": 1715054321000
    }
  ],
  "count": 1
}
```
