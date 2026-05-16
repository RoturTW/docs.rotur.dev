# Get a Sub-Token

> **Authentication:** Required (requires `tokens:manage` permission)

### GET `/tokens/:id`

**Description:**
Retrieve a single sub-token by its ID.

**Path Parameter:**
* `:id` — the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `auth` — your rotur user token (required)

**Example:**

```http
GET /tokens/st_abc123?auth=YOUR_TOKEN
```

**Response (200):**

```json
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
```

**Error Responses:**

| Status | Condition |
|---|---|
| 404 | Token not found |
