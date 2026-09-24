# Delete a device

## DELETE `/notify/device/:device_id`

Remove a registered device. On v2 the path is `DELETE /v2/notify/devices/:device_id`.

**Auth:** Required. Sub-tokens need `account:settings`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `device_id` | path | string | Yes | The device ID returned by [register](register-endpoint.md) or [check](check-registration.md) |

### Example

```http
DELETE /notify/device/a4f8b2c1d3e5f7a9b0c2d4e6f8a0b2c4
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "device removed",
  "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6f8a0b2c4"
}
```

### Errors

| Status | When |
| --- | --- |
| `404` | `device not found` |
| `500` | `failed to save notification endpoints` |
