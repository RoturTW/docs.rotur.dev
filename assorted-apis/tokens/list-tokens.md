# List All Sub-Tokens

> **Authentication:** Required (requires `tokens:manage` permission)

### GET `/tokens`

**Description:**
List all sub-tokens for your account, including revoked and expired ones.

**Query Parameters:**
* `auth` — your rotur user token (required)

**Example:**

```http
GET /tokens?auth=YOUR_TOKEN
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
      "expires_at": null,
      "token": "the token value",
      "revoked": false,
      "revoked_at": null,
      "origin": "https://myapp.example.com",
      "description": "Read-only access for My App",
      "websites": ["https://myapp.example.com"]
    }
  ],
  "total": 1
}
```
