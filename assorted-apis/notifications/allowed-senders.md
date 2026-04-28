# Allowed Senders

By default, **nobody** can send you notifications. You must explicitly allow users on a per-source basis. Each allowed entry also tracks how many times that user has sent you a notification.

## List Allowed Senders

### GET `/notify/allowed`

Returns a map of sources to their allowed senders and notification counts.

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

## Allow a Sender

### POST `/notify/allowed/:username`

Grants a user permission to send you notifications from a specific source.

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

You cannot add yourself as an allowed sender.

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
DELETE /notify/allowed/mist?source=originChats
```

**Response (200):**

```json
{
  "message": "sender removed",
  "username": "mist",
  "source": "originChats"
}
```
