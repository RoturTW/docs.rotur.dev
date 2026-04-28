# List Endpoints

### GET `/notify/endpoints`

Returns all registered notification endpoints for the authenticated user.

**Response (200):**

```json
{
  "endpoints": [
    {
      "device_id": "a4f8b2c1d3e5f7a9b0c2d4e6",
      "endpoint": "https://push.example.com/deliver/abc123",
      "source": "originChats",
      "created_at": 1715054321000
    },
    {
      "device_id": "b5c6d7e8f9a0b1c2d3e4f5a6",
      "endpoint": "https://push.other.com/send/xyz",
      "source": "myApp",
      "created_at": 1715055000000
    }
  ],
  "count": 2
}
```
