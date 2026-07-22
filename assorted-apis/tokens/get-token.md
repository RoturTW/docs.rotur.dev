# Get a Sub-Token

Retrieve a single sub-token by its ID.

> **Authentication:** Required. The main account token can fetch any of its sub-tokens. A sub-token can only fetch its own info (its `:id` must match).

### GET `/tokens/:id`

**Path Parameter:**
* `:id`: the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `auth`: your rotur user token (required)

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
  "token": "rotur_st_xYz123...",
  "revoked": false,
  "origin": "https://myapp.example.com",
  "description": "Read-only access for My App",
  "websites": ["https://myapp.example.com"]
}
```

`expires_at`, `revoked_at`, and `last_used_at` are omitted when they are not set.

**Error Responses:**

| Status | Condition |
|---|---|
| 403 | A sub-token tried to view a token other than itself |
| 404 | Token not found |
