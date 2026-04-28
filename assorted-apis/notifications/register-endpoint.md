# Register an Endpoint

### POST `/notify/register`

Registers a push notification endpoint for the authenticated user. If the device (identified by the fingerprint + source combination) already exists, its endpoint URL is updated instead of creating a duplicate.

**Body (JSON):**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `endpoint` | string | Yes | The push URL to deliver notifications to (must be `http://` or `https://`) |
| `source` | string | Yes | The application/site name (max 64 chars, e.g. `originChats`) |
| `fingerprint` | string | Yes | A stable device fingerprint (e.g. hash of user-agent + screen + timezone) |

**Request:**

```json
{
  "endpoint": "https://push.example.com/deliver/abc123",
  "source": "originChats",
  "fingerprint": "a1b2c3d4e5f6"
}
```

**Response (200):**

```json
{
  "message": "endpoint registered",
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6",
  "source": "originChats",
  "updated": false
}
```

The `device_id` is server-generated and deterministic. Persist it client-side so you can check registration status or delete the device later. The `updated` field is `true` when an existing endpoint was updated rather than newly created.

Users are limited to **20 registered endpoints**.
