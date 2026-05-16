# Create a Sub-Token

> **Authentication:** Required (main account token only — sub-tokens cannot create other sub-tokens)

### POST `/tokens/create`

**Description:**
Create a new sub-token with a name, set of permissions, and optional expiry. The full token value is only returned in this response — it cannot be retrieved later.

**Query Parameters:**
* `auth` — your rotur user token (required, must be the main account token)

**Request Body (JSON):**

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | A label for this token (1–50 characters) |
| `permissions` | string[] | Yes | At least one valid permission string. See [Permissions](permissions.md) for the full list. |
| `expires_in_hrs` | int | No | Hours until the token expires. Max 8760 (1 year). Omit for no expiry. |
| `origin` | string | No | The origin/app this token is for |
| `description` | string | No | A description of what this token is used for |
| `websites` | string[] | No | Websites associated with this token |

**Example:**

```http
POST /tokens/create?auth=YOUR_MAIN_TOKEN
Content-Type: application/json

{
  "name": "My App",
  "permissions": ["account:view", "posts:view", "posts:create"],
  "expires_in_hrs": 720,
  "origin": "https://myapp.example.com",
  "description": "Read-only + posting access for My App",
  "websites": ["https://myapp.example.com"]
}
```

**Response (201):**

```json
{
  "id": "st_aBcDeFgH...",
  "name": "My App",
  "token": "rotur_st_xYz123...",
  "permissions": ["account:view", "posts:view", "posts:create"],
  "created_at": 1715512345678,
  "expires_at": 1715778745678,
  "origin": "https://myapp.example.com",
  "description": "Read-only + posting access for My App",
  "websites": ["https://myapp.example.com"]
}
```

> ⚠️ **Save the `token` value now.** It is never returned again in any API response.

**Error Responses:**

| Status | Condition |
|---|---|
| 400 | `name` is missing or longer than 50 characters |
| 400 | `permissions` is empty or contains an invalid permission string |
| 400 | Attempting to grant `tokens:manage` (forbidden on sub-tokens) |
| 400 | `expires_in_hrs` exceeds 8760 |
| 400 | Maximum of 25 active sub-tokens reached |
| 403 | Authenticated with a sub-token instead of the main account token |
