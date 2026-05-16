# Update a Sub-Token

> **Authentication:** Required (main account token only — sub-tokens cannot modify other sub-tokens)

### PATCH `/tokens/:id`

**Description:**
Update a sub-token's name, permissions, description, or associated websites. You cannot update a revoked token.

**Path Parameter:**
* `:id` — the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `auth` — your rotur user token (required, must be the main account token)

**Request Body (JSON):**

All fields are optional — only include the fields you want to change.

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | No | New name (1–50 characters) |
| `permissions` | string[] | No | New permission set (replaces all existing permissions) |
| `description` | string | No | New description |
| `websites` | string[] | No | New list of associated websites |

**Example:**

```http
PATCH /tokens/st_abc123?auth=YOUR_MAIN_TOKEN
Content-Type: application/json

{
  "name": "My App - Updated",
  "permissions": ["account:view", "posts:view", "posts:create", "posts:reply"],
  "description": "Read and post access for My App"
}
```

**Response (200):**

```json
{
  "id": "st_abc123",
  "name": "My App - Updated",
  "permissions": ["account:view", "posts:view", "posts:create", "posts:reply"],
  "created_at": 1715512345678,
  "last_used_at": 1715599999999,
  "expires_at": null,
  "revoked": false,
  "revoked_at": null,
  "origin": "https://myapp.example.com",
  "description": "Read and post access for My App",
  "websites": ["https://myapp.example.com"]
}
```

**Error Responses:**

| Status | Condition |
|---|---|
| 400 | Attempting to update a revoked token |
| 400 | `name` is empty or longer than 50 characters |
| 400 | `permissions` contains an invalid permission string |
| 400 | Attempting to grant `tokens:manage` (forbidden on sub-tokens) |
| 403 | Authenticated with a sub-token instead of the main account token |
| 404 | Token not found |
