# Create a token

Create a sub-token with a name, a set of permissions and an optional expiry.

## POST `/tokens/create`

Creates a sub-token and returns its value. The v2 path is `POST /v2/tokens`.

**Auth:** Required. Main token only.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | body | string | Yes | A label for the token, 1–50 characters |
| `permissions` | body | string[] | Yes | At least one permission. See [Permissions](permissions.md). `tokens:manage` is not allowed. |
| `expires_in_hrs` | body | integer | No | Hours until the token expires, up to 8760 (1 year). Omit it, or send `0`, for no expiry. |
| `origin` | body | string | No | The origin or app the token is for |
| `description` | body | string | No | What the token is used for |
| `websites` | body | string[] | No | Websites associated with the token |

### Example

```http
POST /tokens/create
Authorization: Bearer <main token>
Content-Type: application/json

{
  "name": "My App",
  "permissions": ["account:view", "posts:view", "posts:create"],
  "expires_in_hrs": 720,
  "origin": "https://myapp.example.com",
  "description": "Read and post access for My App",
  "websites": ["https://myapp.example.com"]
}
```

**Response `201`:**

```json
{
  "id": "st_aBcDeFgH...",
  "name": "My App",
  "token": "rotur_st_xYz123...",
  "permissions": ["account:view", "posts:view", "posts:create"],
  "created_at": 1715512345678,
  "expires_at": 1718104345678,
  "origin": "https://myapp.example.com",
  "description": "Read and post access for My App",
  "websites": ["https://myapp.example.com"]
}
```

Timestamps are Unix milliseconds. `expires_at`, `origin`, `description` and `websites` are left out when they are empty.

{% hint style="warning" %}
Treat the `token` value like a password: anyone who has it can act with its permissions. The list and get endpoints also return it, so keep your main token safe too.
{% endhint %}

### Errors

| Status | When |
| --- | --- |
| `400` | The body is not valid JSON |
| `400` | `name` is missing or longer than 50 characters |
| `400` | `permissions` is empty or contains an unknown permission |
| `400` | `permissions` contains `tokens:manage` |
| `400` | `expires_in_hrs` is more than 8760 |
| `400` | The account already has 25 active sub-tokens |
| `403` | You authenticated with a sub-token |
