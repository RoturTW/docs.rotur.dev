# List Active Sub-Tokens

List only the sub-tokens that are still usable: not revoked and not expired.

> **Authentication:** Required. Needs the `tokens:manage` permission, which sub-tokens can never hold, so in practice this is main-token only.

### GET `/tokens/active`

**Query Parameters:**
* `auth`: your rotur user token (required)

**Example:**

```http
GET /tokens/active?auth=YOUR_TOKEN
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
