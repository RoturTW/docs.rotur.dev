# Allowed Senders

By default, nobody can send you notifications. You allow senders on a per-source basis, and each allowed entry tracks how many notifications that user has sent you.

## List Allowed Senders

### GET `/notify/allowed`

Returns a map of sources to their allowed senders and notification counts.

**Example:**

```
GET /notify/allowed?auth=your_auth_key
```

**Response (200):**

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

Senders are sorted alphabetically by username.

## Allow a Sender

### POST `/notify/allowed/:username`

Grants a user permission to send you notifications from a specific source.

{% hint style="info" %}
On v2 this is `PUT /v2/notify/allowed/:username`.
{% endhint %}

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `username` | The Rotur username to allow |

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `source` | string | Yes | The source to grant permission for |

**Request:**

```json
{
  "source": "originChats"
}
```

**Response (200):**

```json
{
  "message": "sender allowed",
  "username": "mist",
  "source": "originChats"
}
```

**Common Errors:**

| Status | Body | Condition |
| --- | --- | --- |
| 400 | `{"error": "source is required"}` | Missing or invalid body |
| 404 | `{"error": "user not found"}` | Username does not exist |

## Remove a Sender

### DELETE `/notify/allowed/:username`

Revokes a user's permission to send you notifications from a specific source.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `username` | The Rotur username to revoke |

**Query Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| `source` | Yes | The source to revoke permission from |

**Example:**

```
DELETE /notify/allowed/mist?source=originChats&auth=your_auth_key
```

**Response (200):**

```json
{
  "message": "sender removed",
  "username": "mist",
  "source": "originChats"
}
```

**Common Errors:**

| Status | Body | Condition |
| --- | --- | --- |
| 400 | `{"error": "username and source are required"}` | Missing `source` query parameter |
| 404 | `{"error": "user not found"}` | Username does not exist |
