# List tokens

List every sub-token on your account, including revoked and expired ones.

## GET `/tokens`

Returns all sub-tokens with their values.

**Auth:** Required. Needs `tokens:manage`, which sub-tokens cannot hold, so only the main token works.

### Example

```http
GET /tokens
Authorization: Bearer <main token>
```

**Response `200`:**

```json
{
  "tokens": [
    {
      "id": "st_abc123",
      "name": "My App",
      "permissions": ["account:view", "posts:view"],
      "created_at": 1715512345678,
      "last_used_at": 1715599999999,
      "revoked": false,
      "token": "rotur_st_xYz123...",
      "origin": "https://myapp.example.com",
      "description": "Read-only access for My App",
      "websites": ["https://myapp.example.com"]
    }
  ],
  "total": 1
}
```

`last_used_at`, `expires_at`, `revoked_at`, `origin`, `description` and `websites` are left out when they are empty.
