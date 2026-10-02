# List active tokens

List the sub-tokens that still work: not revoked and not expired.

## GET `/tokens/active`

Returns the active sub-tokens with their values.

**Auth:** Required. Needs `tokens:manage`, which sub-tokens cannot hold, so only the main token works.

### Example

```http
GET /tokens/active
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

Each token has the same fields as in [List tokens](list-tokens.md).
