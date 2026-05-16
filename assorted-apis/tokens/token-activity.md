# Token Activity

> **Authentication:** Required (requires `tokens:manage` permission)

### GET `/tokens/:id/activity`

**Description:**
Get the current status and activity information for a sub-token. This provides a computed status rather than raw fields — useful for checking whether a token is still usable.

**Path Parameter:**
* `:id` — the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `auth` — your rotur user token (required)

**Example:**

```http
GET /tokens/st_abc123/activity?auth=YOUR_TOKEN
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
