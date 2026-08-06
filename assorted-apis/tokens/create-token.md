# Create a Sub-Token

Create a new sub-token with a name, a set of permissions, and an optional expiry.

> **Authentication:** Required (main account token only, sub-tokens cannot create other sub-tokens)

### POST `/tokens/create`

**Query Parameters:**
* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Request Body (JSON):**

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | A label for this token (1-50 characters) |
| `permissions` | string[] | Yes | At least one valid permission string. See [Permissions](permissions.md) for the full list. |
| `expires_in_hrs` | int | No | Hours until the token expires. Max 8760 (1 year). Omit for no expiry. |
| `origin` | string | No | The origin/app this token is for |
| `description` | string | No | A description of what this token is used for |
| `websites` | string[] | No | Websites associated with this token |

**Example:**

```http
POST /tokens/create
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

{% hint style="warning" %}
Save the `token` value now. You can also see it later via the listing endpoints, but treat it like a password: anyone who has it can act with this token's permissions.
{% endhint %}

**Error Responses:**

| Status | Condition |
|---|---|
| 400 | `name` is missing or longer than 50 characters |
| 400 | `permissions` is empty or contains an invalid permission string |
| 400 | Attempting to grant `tokens:manage` (forbidden on sub-tokens) |
| 400 | `expires_in_hrs` exceeds 8760 |
| 400 | Maximum of 25 active sub-tokens reached |
| 403 | Authenticated with a sub-token instead of the main account token |
