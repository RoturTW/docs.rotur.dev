# Check Registration

### GET `/notify/check`

Checks whether a specific device is already registered for a given source.

**Query Parameters:**

| Parameter | Required | Description |
| --- | --- | --- |
| `source` | Yes | The application/source name |
| `fingerprint` | Yes | The device fingerprint used during registration |

**Example:**

```
GET /notify/check?source=originChats&fingerprint=a1b2c3d4e5f6
```

**Response (registered):**

```json
{
  "registered": true,
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6",
  "endpoint": "https://push.example.com/deliver/abc123",
  "p256dh": "BASE64URL_P256DH_KEY",
  "auth": "BASE64URL_AUTH_SECRET",
  "source": "originChats",
  "created_at": 1715054321000
}
```

**Response (not registered):**

```json
{
  "registered": false,
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6"
}
```

The `device_id` is always returned so the client can persist it without needing to register first.
