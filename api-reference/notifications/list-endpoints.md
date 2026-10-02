# List endpoints

## GET `/notify/endpoints`

List every push endpoint registered on your account, across all sources.

**Auth:** Required. Sub-tokens need `notifications:view`.

### Example

```http
GET /notify/endpoints
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "endpoints": [
    {
      "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6f8a0b2c4",
      "endpoint": "https://push.example.com/deliver/abc123",
      "p256dh": "BASE64URL_P256DH_KEY",
      "auth": "BASE64URL_AUTH_SECRET",
      "source": "originChats",
      "created_at": 1715054321000
    },
    {
      "device_id": "b5c6d7e8f9a0b1c2d3e4f5a6b7c8d9e0",
      "endpoint": "https://push.other.com/send/xyz",
      "p256dh": "BASE64URL_P256DH_KEY_2",
      "auth": "BASE64URL_AUTH_SECRET_2",
      "source": "rotur.dev",
      "created_at": 1715055000000
    }
  ],
  "count": 2
}
```

`created_at` is when the endpoint was last registered (Unix ms). Endpoints the push service rejects are removed automatically, so a device can disappear from this list without you deleting it.
