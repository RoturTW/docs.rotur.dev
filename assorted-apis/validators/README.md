# Validators

A validator is a short-lived string that proves a user owns a Rotur account, without your service ever seeing their token or password. The user generates a validator for your app's key and hands it to your service, which checks it and gets back the user's username and ID.

> **Base URL:** `https://api.rotur.dev`
>
> **Auth:** Generating a validator needs the user's token in the `Authorization: Bearer <token>` header (the legacy `auth` query parameter is also accepted). Sub-tokens need `validators:generate`. Validating is public.

The v2 paths are `POST /v2/validators` (generate) and `GET /v2/validators/verify` (validate). They take the same query parameters.

## GET `/generate_validator`

Generates a validator for the signed-in user, bound to `key`. It is valid for 5 minutes.

**Auth:** Required. Sub-tokens need `validators:generate`.

If you authenticate with a sub-token, the server still uses the main account token to compute the hash, so validation works the same either way.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `key` | query | string | Yes | Your application's key, bound into the validator hash |

### Example

```http
GET /generate_validator?key=myAppKey
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "validator": "<userId>,<hash>"
}
```

`validator` is the user's ID and a SHA-256 hash, separated by a comma.

### Errors

| Status | When |
| --- | --- |
| `400` | `key` is missing |
| `403` | The token is missing or invalid, or a sub-token lacks `validators:generate` |
| `403` | The account is blocked or banned. The body has `code: "account_blocked"`, `standing`, `recover_at`, `reason` and `redirect_url`. |

## GET `/validate`

Checks a validator against a key. Your service calls this; no authentication is needed.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `v` | query | string | Yes | The full validator string, `userId,hash` |
| `key` | query | string | Yes | The key the validator was generated for |

### Example

```http
GET /validate?v=abc123def456,a1b2c3d4e5f6...&key=myAppKey
```

**Response `200` (valid):**

```json
{
  "valid": true,
  "username": "example_user",
  "id": "abc123def456"
}
```

**Response `200` (not valid):**

An expired, unknown or wrong-key validator still returns `200`, with `valid: false`. Always check `valid`.

```json
{
  "valid": false,
  "error": "Validator expired or not found"
}
```

```json
{
  "valid": false,
  "error": "Invalid validator"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `v` or `key` is missing |
| `400` | `v` has no comma (`Invalid validator format`) |
| `400` | The user has no token set |
| `403` | The account is blocked or banned. The body has `valid: false`, `code: "account_blocked"`, `username` and `id`. |
| `404` | No user has the ID in the validator |

## How it works

### Hash

```
SHA-256(key + authKey + windowStart)
```

- `key`: your application's key.
- `authKey`: the user's main account token. It never leaves the server.
- `windowStart`: the generation time in Unix seconds rounded down to a multiple of 300, as a decimal string.

The result is a 64-character lowercase hex string.

### Lifetime

Each validator is valid for 300 seconds from the moment it was generated. The hash uses the rounded window start, but expiry counts from the exact generation time, so every validator gets the full 5 minutes.

### Validation steps

1. Split `v` into the user ID and the hash.
2. Look up the user and their main account token.
3. Find a stored validator for that user with the same hash that has not expired.
4. Recompute the hash from `key`, the user's token and that validator's window.
5. If it matches, the validator is valid.

A validator can be checked more than once until it expires.

### Storage

Validators are held in memory only. Expired ones are pruned every 300 seconds and whenever the same user generates a new one. A server restart invalidates every pending validator, and so does refreshing the user's main token.

## Security

- Each validator expires 5 minutes after it is generated, which limits replay.
- The hash includes both your key and the user's account token, so a validator cannot be reused for another key or user.
- The account token is never returned; it is only mixed into the hash on the server.
- Anyone can call `/validate`. Forging a validator would require the user's account token.
