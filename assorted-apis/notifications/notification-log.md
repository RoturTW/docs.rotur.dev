# Notification log

## GET `/notify/log`

List the notifications sent to you through the notify service, oldest first.

**Auth:** Required. Sub-tokens need `notifications:view`.

Every notification sent to you is logged, whether or not it was pushed to a device. The log has a size limit that depends on your subscription; when it is full, the oldest entries are dropped.

| Plan | Log size |
| --- | --- |
| Free | 256 KB |
| Lite | 1 MB |
| Plus | 2 MB |
| Pro | 10 MB |
| Max | 50 MB |

### Example

```http
GET /notify/log
Authorization: Bearer <token>
```

**Response `200`:**

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

`from` is the sender's username, or the `data.from` value they set. `title` and `body` are left out when empty. `at` is a Unix timestamp in milliseconds.
