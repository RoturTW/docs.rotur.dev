# Create a token

Create a sub-token with a name, a set of permissions and an optional expiry.

## POST `/tokens/create`

Creates a sub-token and returns its value. The v2 path is `POST /v2/tokens`.

**Auth:** Required. Main token only.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | body | string | Yes | A label for the token, 1–50 characters |
| `permissions` | body | string[] | Yes | At least one permission. See [Permissions](permissions.md). `tokens:manage` is not allowed. `account:email` and `account:signins` are dropped if the account is under 18 or has no date of birth. |
| `expires_in_hrs` | body | integer | No | Hours until the token expires, up to 8760 (1 year). Omit it, or send `0`, for no expiry. |
| `origin` | body | string | No | The origin or app the token is for |
| `description` | body | string | No | What the token is used for |
| `websites` | body | string[] | No | Websites associated with the token |
| `password` | body | string | Sometimes | Your account password. Needed when `permissions` includes one that [needs confirmation](permissions.md#permissions-that-need-confirmation). |

If your account doesn't sign in with a password (you use a passkey or another provider), you confirm instead by having signed in within the last 10 minutes, using the main token.

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

Timestamps are Unix milliseconds. `expires_at`, `origin`, `description` and `websites` are left out when they are empty. `permissions` is what the token actually holds, so check it if you asked for `account:email` or `account:signins`.

Before counting your active tokens, the server deletes any you haven't used for 30 days, so they don't hold up the limit.

{% hint style="warning" %}
Treat the `token` value like a password: anyone who has it can act with its permissions. The list and get endpoints also return it, so keep your main token safe too.
{% endhint %}

### Errors

| Status | When |
| --- | --- |
| `400` | The body is not valid JSON (`Invalid request body`) |
| `400` | `name` is missing or longer than 50 characters |
| `400` | `permissions` is empty or contains an unknown permission (`Invalid permission: <permission>`) |
| `400` | `permissions` contains `tokens:manage` |
| `400` | A permission needs confirmation and you didn't send `password` (`code: "password_required"`) |
| `400` | `expires_in_hrs` is more than 8760 |
| `400` | The account already has 250 active sub-tokens |
| `403` | You authenticated with a sub-token |
| `403` | `password` is wrong (`code: "password_incorrect"`) |
| `403` | Your account doesn't sign in with a password and you haven't signed in within 10 minutes (`code: "reauth_required"`) |
| `429` | Too many password attempts. You get 10 every 10 minutes. (`code: "rate_limited"`) |
