# Get a token

Get one sub-token by its ID.

## GET `/tokens/:id`

Returns the sub-token, including its value. A sub-token can call this with its own ID to read its own name and permissions.

**Auth:** Required. The main token can get any of its sub-tokens. A sub-token can only get itself.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The sub-token ID, for example `st_abc123` |

### Example

```http
GET /tokens/st_abc123
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "id": "st_abc123",
  "name": "My App",
  "permissions": ["account:view", "posts:view"],
  "created_at": 1715512345678,
  "last_used_at": 1715599999999,
  "revoked": false,
  "token": "rotur_st_xYz123...",
  "origin": "https://myapp.example.com",
  "description": "Read-only access for My App",
  "websites": ["https://myapp.example.com"]
}
```

`last_used_at`, `expires_at`, `revoked_at`, `origin`, `description` and `websites` are left out when they are empty.

### Errors

| Status | When |
| --- | --- |
| `403` | A sub-token asked for a different sub-token |
| `404` | No sub-token with this ID exists on the account |
