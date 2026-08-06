# /notifications

Returns your recent notification events: follows, replies, likes, mentions, reposts, item sales, and more.

Requires authentication and the `notifications:view` permission.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| after | No | How many days to look back. Must be a whole number of 1 or more. Default 1 |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/notifications?after=7"
```

## Response

Every event has `type`, `id`, and `timestamp`, plus fields specific to its type:

```json
[
  {
    "type": "reply",
    "id": "e5f6a7b8",
    "timestamp": 1715054400000,
    "post_id": "abc123",
    "reply_id": "def456",
    "user": "user_id",
    "content": "Nice post"
  },
  {
    "type": "follow",
    "id": "a1b2c3d4",
    "timestamp": 1715054321000,
    "follower": "user_id"
  },
  {
    "type": "like",
    "id": "c9d0e1f2",
    "timestamp": 1715054300000,
    "post_id": "abc123",
    "user": "user_id"
  }
]
```

Events are sorted newest first. The server keeps at most 100 events per user.

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Invalid time period` | `after` is not a whole number of 1 or more |
