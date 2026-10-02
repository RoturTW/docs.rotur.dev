# Send a notification

Send a notification to one user or to a list of users for a source. Each notification is added to the recipient's [notification log](notification-log.md) and [notification feed](../../claw/api-endpoints/notifications.md), and is pushed to their devices when they allow it (see [Who can push to you](README.md#who-can-push-to-you)).

## POST `/notify/:username`

Send a notification to one user. On v2 the path is `POST /v2/notify/users/:username`.

**Auth:** Required. Sub-tokens need `notifications:send`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The user to notify |
| `source` | body | string | Yes | The source the notification belongs to |
| `title` | body | string | No | Notification title, up to 256 bytes |
| `body` | body | string | No | Notification text, up to 1024 bytes |
| `data` | body | object | No | Any JSON object. Delivered in the push payload and echoed in the response. A string `data.from` replaces your username as the displayed sender |

### Example

```http
POST /notify/rm
Authorization: Bearer <token>
Content-Type: application/json

{
  "source": "originChats",
  "title": "New message",
  "body": "Hey, are you online?",
  "data": { "channel": "general" }
}
```

**Response `200`:**

```json
{
  "success": true,
  "message": "notification sent",
  "title": "New message",
  "body": "Hey, are you online?",
  "data": { "channel": "general" },
  "pushed": true
}
```

`pushed` is `true` when the notification was queued for delivery to at least one device. It is `false` when the recipient has not allowed you for the source, has blocked you from notifications, has push turned off, or has no device for the source or for `rotur.dev`. The request still succeeds in those cases.

### Push payload

Each target device receives this JSON as the push message data:

```json
{
  "type": "notification",
  "from": "mist",
  "sender": "mist",
  "source": "originChats",
  "title": "New message",
  "body": "Hey, are you online?",
  "sent_at": 1715054321000,
  "data": { "channel": "general" }
}
```

| Field | Description |
| --- | --- |
| `type` | Always `notification` |
| `from` | The name to show as the sender: `data.from` if you set it, otherwise your username |
| `sender` | The username of the account that sent the push. Always your username, even when `data.from` is set |
| `source` | The source from the request |
| `title` / `body` | From the request. Empty strings when not set |
| `sent_at` | When the push was sent (Unix ms) |
| `data` | Your `data` object. Omitted when you did not send one or it is empty |

The push goes to the recipient's devices registered for `source`. If they have none for that source, it goes to their devices registered under `rotur.dev` instead, so a service worker on rotur.dev can receive pushes for other sources.

{% hint style="warning" %}
Pushes are sent with a 30 second TTL, so a device that is offline for longer may never receive them. Delivery happens after the response is returned. Devices whose push service rejects the message are removed from the recipient's endpoints.
{% endhint %}

### Errors

| Status | When |
| --- | --- |
| `400` | `source is required`: the body is missing or invalid, or has no `source` |
| `400` | `title too long (max 256 chars)` |
| `400` | `body too long (max 1024 chars)` |
| `403` | `cannot send notification to this user`: the recipient has blocked you as a user |
| `404` | `target user not found` |

## POST `/notify/`

Send the same notification to several users. On v2 the path is `POST /v2/notify`, `POST /v2/notify/` or `POST /v2/notify/users`.

**Auth:** Required. Sub-tokens need `notifications:send`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `source` | body | string | Yes | The source the notification belongs to |
| `users` | body | string[] | No | Usernames to notify |
| `title` | body | string | No | Notification title, up to 256 bytes |
| `body` | body | string | No | Notification text, up to 1024 bytes |
| `data` | body | object | No | Any JSON object, handled the same way as for a single user |

Users who do not exist or have blocked you as a user are skipped and do not appear in `results`. Everyone else gets the notification and a result entry, with `pushed` showing whether it reached a device.

### Example

```http
POST /notify/
Authorization: Bearer <token>
Content-Type: application/json

{
  "source": "originChats",
  "users": ["mist", "rm"],
  "title": "Server maintenance",
  "body": "originChats will restart in 5 minutes"
}
```

**Response `200`:**

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
        "data": null,
        "pushed": true
      }
    },
    {
      "username": "rm",
      "code": 200,
      "result": {
        "success": true,
        "message": "notification sent",
        "title": "Server maintenance",
        "body": "originChats will restart in 5 minutes",
        "data": null,
        "pushed": false
      }
    }
  ]
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `source is required`: the body is missing or invalid, or has no `source` |
| `400` | `title too long (max 256 chars)` |
| `400` | `body too long (max 1024 chars)` |
