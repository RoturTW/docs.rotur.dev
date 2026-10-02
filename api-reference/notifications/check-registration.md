# Check registration

## GET `/notify/check`

Check whether this device is registered for a source.

**Auth:** Required. Sub-tokens need `notifications:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `source` | query | string | Yes | The source name |
| `fingerprint` | query | string | Yes | The fingerprint you register the device with |

### Example

```http
GET /notify/check?source=originChats&fingerprint=a1b2c3d4e5f6
Authorization: Bearer <token>
```

**Response `200` (registered):**

```json
{
  "registered": true,
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6f8a0b2c4",
  "endpoint": "https://push.example.com/deliver/abc123",
  "source": "originChats",
  "created_at": 1715054321000
}
```

**Response `200` (not registered):**

```json
{
  "registered": false,
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6f8a0b2c4"
}
```

`device_id` is always returned, so you can learn it without registering first. `created_at` is when the endpoint was last registered (Unix ms).

### Errors

| Status | When |
| --- | --- |
| `400` | `source and fingerprint query params are required` |
