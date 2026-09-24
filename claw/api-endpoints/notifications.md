# Notifications

Your in-app notification feed. Rotur adds a notification when someone follows you, replies to, likes, mentions or reposts you, buys your item and so on. Notifications that apps send you with [send notification](../../assorted-apis/notifications/send-notification.md) are added here too, with `type` `notification`.

> **Auth:** Every endpoint requires a token. Sub-tokens need `notifications:view`, including for marking notifications read and deleting them.

The server keeps your 100 most recent notifications; older ones are dropped. New notifications are also pushed live over the status and Claw WebSockets.

## GET `/notifications`

Returns your notifications from the last `after` days, newest first.

**Auth:** Required. Sub-tokens need `notifications:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `after` | query | integer | No | How many days back to look, 1 or more. Default 30 |

### Example

```http
GET /notifications?after=7
Authorization: Bearer <token>
```

**Response `200`:**

```json
[
  {
    "type": "reply",
    "id": "e5f6a7b8",
    "timestamp": 1715054400000,
    "created": 1715054400000,
    "read": false,
    "post_id": "abc123",
    "reply_id": "def456",
    "user": "rm",
    "content": "Nice post"
  },
  {
    "type": "follow",
    "id": "a1b2c3d4",
    "timestamp": 1715054321000,
    "created": 1715054321000,
    "read": true,
    "follower": "rm"
  },
  {
    "type": "notification",
    "id": "f1e2d3c4",
    "timestamp": 1715054300000,
    "created": 1715054300000,
    "read": false,
    "from": "rm",
    "source": "originChats",
    "title": "New message",
    "body": "rm: hello",
    "actor": "rm",
    "platform": "originChats"
  }
]
```

Every notification has `type`, `id`, `timestamp`, `created` (same as `timestamp`) and `read`, plus fields for its type. Fields that name a user, such as `user`, `follower` and `from`, hold usernames, not user IDs. Notifications sent by apps have `from`, `source`, `title` and, if set, `body`, plus `actor` (the sender), `platform` (the source) and `platform_data` (the data the app attached).

### Errors

| Status | When |
| --- | --- |
| `400` | `Invalid time period` (`after` is not a whole number of 1 or more) |

## POST `/notifications/read`

Marks notifications as read: the ones you list, or all of them if you send no IDs.

**Auth:** Required. Sub-tokens need `notifications:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `ids` | body | string[] | No | IDs of the notifications to mark read. Leave it out, or send an empty array or no body, to mark all of them read |

### Example

```http
POST /notifications/read
Authorization: Bearer <token>
Content-Type: application/json

{ "ids": ["e5f6a7b8", "f1e2d3c4"] }
```

**Response `200`:**

```json
{
  "success": true,
  "updated": 2
}
```

`updated` counts only notifications that were unread before. Unknown IDs are ignored.

## PUT `/notifications/:id/read`

Marks one notification as read.

**Auth:** Required. Sub-tokens need `notifications:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | ID of the notification |

### Example

```http
PUT /notifications/e5f6a7b8/read
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "success": true
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | `notification not found`. This is also returned if the notification is already read |

## DELETE `/notifications/:id`

Deletes one notification.

**Auth:** Required. Sub-tokens need `notifications:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | ID of the notification |

### Example

```http
DELETE /notifications/e5f6a7b8
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "success": true
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | `notification not found` |

## DELETE `/notifications`

Deletes all of your notifications.

**Auth:** Required. Sub-tokens need `notifications:view`.

{% hint style="warning" %}
This clears your whole notification feed and cannot be undone.
{% endhint %}

### Example

```http
DELETE /notifications
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "success": true,
  "removed": 12
}
```
