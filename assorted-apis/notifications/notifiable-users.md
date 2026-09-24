# Notifiable users

## GET `/notify/:source/users`

List every user who has allowed you to send them notifications for a source. On v2 the path is `GET /v2/notify/sources/:source/users`.

**Auth:** Required. Sub-tokens need `notifications:view`.

The list is based on allowed senders only. It does not check whether the user has blocked you, turned push off, or registered any devices.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `source` | path | string | Yes | The source to check |

### Example

```http
GET /notify/originChats/users
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "success": true,
  "source": "originChats",
  "users": [
    { "username": "mist", "id": "abc123" },
    { "username": "rm", "id": "def456" }
  ]
}
```

`users` is `null`, not an empty array, when nobody has allowed you for the source.
