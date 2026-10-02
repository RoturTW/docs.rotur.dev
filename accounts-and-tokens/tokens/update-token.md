# Update a token

Change a sub-token's name, permissions, description or websites.

## PATCH `/tokens/:id`

Updates the fields you send and leaves the rest unchanged. You cannot update a revoked token. You can't change a token's expiry; create a new token instead.

**Auth:** Required. Main token only.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The sub-token ID, for example `st_abc123` |
| `name` | body | string | No | New name, 1–50 characters |
| `permissions` | body | string[] | No | New permissions. Replaces the existing list. `tokens:manage` is not allowed. `account:email` and `account:signins` are dropped if the account is under 18 or has no date of birth. |
| `description` | body | string | No | New description |
| `websites` | body | string[] | No | New list of websites. Replaces the existing list. |
| `password` | body | string | Sometimes | Your account password. Needed when you add a permission that [needs confirmation](permissions.md#permissions-that-need-confirmation) and the token didn't already hold. Works the same as in [Create a token](create-token.md). |

For an [OAuth access token](README.md#oauth-access-tokens), changing `permissions` has no effect: it always holds only what its scopes give.

### Example

```http
PATCH /tokens/st_abc123
Authorization: Bearer <main token>
Content-Type: application/json

{
  "name": "My App - Updated",
  "permissions": ["account:view", "posts:view", "posts:create", "posts:reply"],
  "description": "Read and post access for My App"
}
```

**Response `200`:**

The updated token.

```json
{
  "id": "st_abc123",
  "name": "My App - Updated",
  "permissions": ["account:view", "posts:view", "posts:create", "posts:reply"],
  "created_at": 1715512345678,
  "last_used_at": 1715599999999,
  "revoked": false,
  "token": "rotur_st_xYz123...",
  "origin": "https://myapp.example.com",
  "description": "Read and post access for My App",
  "websites": ["https://myapp.example.com"]
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The body is not valid JSON (`Invalid request body`) |
| `400` | `name` is empty or longer than 50 characters |
| `400` | `permissions` contains an unknown permission or `tokens:manage` |
| `400` | The token is revoked (`Cannot update a revoked token`) |
| `400` | You added a permission that needs confirmation without `password` (`code: "password_required"`) |
| `403` | You authenticated with a sub-token |
| `403` | `password` is wrong, or a passwordless account hasn't signed in within 10 minutes (`code: "password_incorrect"` or `"reauth_required"`) |
| `429` | Too many password attempts (`code: "rate_limited"`) |
| `404` | No sub-token with this ID exists on the account |
