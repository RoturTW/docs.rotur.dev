# Allowed and blocked senders

Nobody can push notifications to you until you allow them for a source. Each allowed sender has a count of how many pushes they have delivered to you. Blocking a sender stops their pushes for every source, even if they are allowed.

## GET `/notify/allowed`

List your allowed senders, grouped by source.

**Auth:** Required. Sub-tokens need `notifications:view`.

### Example

```http
GET /notify/allowed
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "originChats": {
    "senders": [
      { "username": "mist", "count": 12 },
      { "username": "rm", "count": 3 }
    ]
  },
  "myApp": {
    "senders": [
      { "username": "temp", "count": 0 }
    ]
  }
}
```

Senders are sorted by username. `count` only goes up when a push is actually delivered.

## POST `/notify/allowed/:username`

Allow a user to push notifications to you for one source. On v2 use `PUT` or `POST /v2/notify/allowed/:username`.

**Auth:** Required. Sub-tokens need `account:settings`.

Allowing a sender who is already allowed resets their `count` to `0`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The user to allow |
| `source` | body | string | Yes | The source to allow them for |

### Example

```http
POST /notify/allowed/mist
Authorization: Bearer <token>
Content-Type: application/json

{
  "source": "originChats"
}
```

**Response `200`:**

```json
{
  "message": "sender allowed",
  "username": "mist",
  "source": "originChats"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `source is required`: the body is missing, invalid, or has no `source` |
| `404` | `user not found` |

## DELETE `/notify/allowed/:username`

Stop allowing a user for one source.

**Auth:** Required. Sub-tokens need `account:settings`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The user to remove |
| `source` | query | string | Yes | The source to remove them from |

### Example

```http
DELETE /notify/allowed/mist?source=originChats
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "sender removed",
  "username": "mist",
  "source": "originChats"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `username and source are required` |
| `404` | `user not found` |

## Blocked senders

A blocked sender's pushes are never delivered to you, whatever source they use and whether or not they are allowed. Their notifications still reach your [notification log](notification-log.md). To reject their notifications entirely, block them as a user instead.

### GET `/notify/blocked`

List the users you blocked from sending you pushes.

**Auth:** Required. Sub-tokens need `notifications:view`.

**Response `200`:**

```json
{
  "blocked": ["spammer", "temp"]
}
```

Usernames are sorted alphabetically.

### PUT `/notify/blocked/:username`

Block a user from sending you pushes. Blocking someone who is already blocked has no effect.

**Auth:** Required. Sub-tokens need `account:settings`.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The user to block |

**Response `200`:**

```json
{
  "success": true
}
```

| Status | When |
| --- | --- |
| `400` | `you cannot block yourself` |
| `404` | `user not found` |

### DELETE `/notify/blocked/:username`

Unblock a user.

**Auth:** Required. Sub-tokens need `account:settings`.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | The user to unblock |

**Response `200`:**

```json
{
  "success": true
}
```

| Status | When |
| --- | --- |
| `404` | `user not found` |
