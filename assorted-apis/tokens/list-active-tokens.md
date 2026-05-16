# List Active Sub-Tokens

> **Authentication:** Required (requires `tokens:manage` permission)

### GET `/tokens/active`

**Description:**
List only active sub-tokens — those that are not revoked and have not expired.

**Query Parameters:**
* `auth` — your rotur user token (required)

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
      "expires_at": null,
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
