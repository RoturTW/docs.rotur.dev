# Token activity

Get a sub-token's computed status, to check whether it still works.

## GET `/tokens/:id/activity`

Returns the sub-token's details and a `status` field. The token value is not included.

**Auth:** Required. Needs `tokens:manage`, which sub-tokens cannot hold, so only the main token works.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The sub-token ID, for example `st_abc123` |

### Example

```http
GET /tokens/st_abc123/activity
Authorization: Bearer <main token>
```

**Response `200`:**

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

Every field is always present; unset timestamps are `null`.

| `status` | Meaning |
| --- | --- |
| `active` | The token works |
| `revoked` | The token was revoked, or it expired and the hourly cleanup has marked it revoked |
| `expired` | The expiry time has passed and the cleanup has not run yet |

### Errors

| Status | When |
| --- | --- |
| `404` | No sub-token with this ID exists on the account |
