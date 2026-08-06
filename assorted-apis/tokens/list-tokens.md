# List All Sub-Tokens

List every sub-token on your account, including revoked and expired ones.

> **Authentication:** Required. Needs the `tokens:manage` permission, which sub-tokens can never hold, so in practice this is main-token only.

### GET `/tokens`

**Query Parameters:**
* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Example:**

```http
GET /tokens
```

**Response (200):**

```json
{
  "tokens": [
    {
      "id": "st_abc123",
      "name": "My App",
      "permissions": ["account:view", "posts:view"],
      "created_at": 1715512345678,
      "last_used_at": 1715599999999,
      "token": "rotur_st_xYz123...",
      "revoked": false,
      "origin": "https://myapp.example.com",
      "description": "Read-only access for My App",
      "websites": ["https://myapp.example.com"]
    }
  ],
  "total": 1
}
```

`expires_at`, `revoked_at`, and `last_used_at` are omitted when they are not set.
