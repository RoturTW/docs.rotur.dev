# Notifiable Users

### GET `/notify/:source/users`

Lists every user who has allowed you to send them notifications from a given source. Useful for finding out who you can actually reach before sending.

{% hint style="info" %}
On v2 this is `GET /v2/notify/sources/:source/users`.
{% endhint %}

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `source` | The source to check |

**Example:**

```
GET /notify/originChats/users?auth=your_auth_key
```

**Response (200):**

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

`users` is `null` when nobody has allowed you for the source.
