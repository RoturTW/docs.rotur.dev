# Delete a Device

### DELETE `/notify/device/:device_id`

Removes a device from your registered endpoints.

{% hint style="info" %}
On v2 this is `DELETE /v2/notify/devices/:device_id`.
{% endhint %}

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

**Common Errors:**

| Status | Body | Condition |
| --- | --- | --- |
| 404 | `{"error": "device not found"}` | No endpoint with that device ID |
