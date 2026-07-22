# Send a Notification

## Send to One User

### POST `/notify/:username`

Sends a push notification to the target user's registered endpoints for the given source. You must be in the target's allowed list for that source, and the target must not have blocked you.

{% hint style="info" %}
On v2 this is `POST /v2/notify/users/:username`.
{% endhint %}

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
| `data` | object | No | Arbitrary JSON, echoed back in the response |

**Request:**

```json
{
  "source": "originChats",
  "title": "New message",
  "body": "Hey, are you online?"
}
```

**Response (200):**

```json
{
  "success": true,
  "message": "notification sent",
  "title": "New message",
  "body": "Hey, are you online?",
  "data": null
}
```

**Push Payload Delivered to Endpoints:**

Each of the target's registered endpoints for the source receives this payload:

```json
{
  "type": "notification",
  "from": "mist",
  "title": "New message",
  "body": "Hey, are you online?",
  "sent_at": 1715054321000
}
```

{% hint style="warning" %}
The `data` field is not included in the push payload. It is only echoed back in the API response.
{% endhint %}

Push delivery is fire-and-forget. The response is returned immediately after dispatch, and endpoints that the push service rejects are pruned automatically.

**Common Errors:**

| Status | Condition |
| --- | --- |
| 400 | Missing `source`, or `title`/`body` too long |
| 403 | You are not in the target's allowed list for this source |
| 403 | The target has blocked you |
| 404 | Target user not found |
| 404 | Target has no registered endpoints for this source |

## Send to Many Users

### POST `/notify/`

Sends the same notification to a list of users in one request.

{% hint style="info" %}
On v2 this is `POST /v2/notify/users`.
{% endhint %}

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `source` | string | Yes | The source the notification is from |
| `users` | string[] | No | Usernames to notify |
| `title` | string | No | Notification title (max 256 chars) |
| `body` | string | No | Notification body text (max 1024 chars) |
| `data` | object | No | Arbitrary JSON, echoed back per result |

Users that do not exist, have blocked you, or have not allowed you for the source are silently skipped and do not appear in the results.

**Request:**

```json
{
  "source": "originChats",
  "users": ["mist", "rm"],
  "title": "Server maintenance",
  "body": "originChats will restart in 5 minutes"
}
```

**Response (200):**

```json
{
  "success": true,
  "results": [
    {
      "username": "mist",
      "code": 200,
      "result": {
        "success": true,
        "message": "notification sent",
        "title": "Server maintenance",
        "body": "originChats will restart in 5 minutes",
        "data": null
      }
    }
  ]
}
```

A per-user `code` of `404` means that user has no registered endpoints for the source.
