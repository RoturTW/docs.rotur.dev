# Token Activity

Get a computed status for a sub-token, useful for checking whether it is still usable.

> **Authentication:** Required. Needs the `tokens:manage` permission, which sub-tokens can never hold, so in practice this is main-token only.

### GET `/tokens/:id/activity`

**Path Parameter:**
* `:id`: the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Example:**

```http
GET /tokens/st_abc123/activity
```

**Response (200):**

```json
{
  "id": "st_abc123",
  "name": "My App",
  "status": "active",
  "permissions": ["account:view", "posts:view"],
  "created_at": 1715512345678,
  "last_used_at": 1715599999999,
  "expires_at": null,
  "revoked_at": null,
  "origin": "https://myapp.example.com",
  "description": "Read-only access for My App",
  "websites": ["https://myapp.example.com"]
}
```

The `status` field can be one of:

| Status | Meaning |
|---|---|
| `active` | Token is usable |
| `revoked` | Token has been manually revoked |
| `expired` | Token's expiry time has passed |

**Error Responses:**

| Status | Condition |
|---|---|
| 404 | Token not found |
