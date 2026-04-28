# Delete a Device

### DELETE `/notify/device/:device_id`

Removes a specific device from the authenticated user's registered endpoints.

**Path Parameters:**

| Parameter | Description |
| --- | --- |
| `device_id` | The server-generated device ID returned during registration |

**Example:**

```
DELETE /notify/device/a4f8b2c1d3e5f7a9b0c2d4e6
```

**Response (200):**

```json
{
  "message": "device removed",
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6"
}
```

**Response (404):**

```json
{
  "error": "device not found"
}
```
