# Validators

Validators are a time-limited, hash-based verification system. They let a third-party service confirm that a user owns a Rotur account and holds a specific key, without ever seeing the user's token or password.

A typical flow: a user generates a validator on Rotur, hands it to your service, and your service calls the validate endpoint to confirm their identity. You get back their username and ID.

> **Base URL:** `https://api.rotur.dev/`
>
> The same endpoints exist under v2: `POST /v2/validators` (generate) and `GET /v2/validators/verify` (validate).

***

### GET `/generate_validator`

Generate a validator string for the authenticated user. The validator is valid for **5 minutes** from the moment it is generated.

> **Authentication:** Required. Sub-tokens need the `validators:generate` permission.

**Query Parameters:**
* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.
* `key`: the application key to bind into the validator hash (required)

{% hint style="info" %}
If you authenticate with a sub-token, the server still uses your main account key when computing the hash. Validation works the same either way.
{% endhint %}

**Example:**

```http
GET /generate_validator?key=myAppKey
```

**Response (200):**

```json
{
  "validator": "<userId>,<hashedValue>"
}
```

The `validator` field is a comma-separated string containing the user's internal ID and a SHA-256 hash.

**Error Responses:**

| Status | Condition |
|--------|-----------|
| 400 | `key` query parameter missing |
| 403 | Missing or invalid `auth`, or sub-token lacks `validators:generate` |

***

### GET `/validate`

Validate a previously generated validator against a key. This endpoint is **unauthenticated**: it is designed to be called by third-party services.

**Query Parameters:**
* `v`: the full validator string (`userId,hashedValue`) (required)
* `key`: the key to verify against the validator (required)

**Example:**

```http
GET /validate?v=abc123def456,a1b2c3d4e5f6...&key=myAppKey
```

**Response (valid):**

```json
{
  "valid": true,
  "username": "example_user",
  "id": "abc123def456"
}
```

**Response (invalid or expired):**

Note that these are returned with status 200.

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

**Error Responses:**

| Status | Condition |
|--------|-----------|
| 400 | `v` query parameter missing |
| 400 | `key` query parameter missing |
| 400 | Malformed validator (no comma) |
| 400 | The user has no token set |
| 404 | User ID in validator does not exist |

***

## How It Works

### Time Windows

Validators use a **5-minute lifetime** (300 seconds). The hash is computed against a rounded window boundary, but the validator remains valid for exactly 5 minutes from the moment it was generated:

```
windowStart = floor(timestamp / 300) x 300
expiresAt   = generatedAt + 300
```

This means a validator always has a full 5-minute validity period regardless of when during a window it was created.

### Hash Computation

The validator hash is computed as:

```
SHA-256(key + authKey + windowStartTimestamp)
```

Where:
- `key` is the application-specific key passed by the user
- `authKey` is the user's main account token (used server-side)
- `windowStartTimestamp` is the Unix timestamp rounded down to the current 300-second window, as a decimal string

The result is a 64-character lowercase hex string.

### Generation Flow

1. The authenticated user calls `/generate_validator` with `key` and `auth`.
2. The server computes `windowStart = floor(now / 300) x 300`.
3. The server computes `hash = SHA-256(key + authKey + windowStart)`.
4. The hash is stored in memory alongside its creation timestamp, keyed by user ID.
5. The response returns `"<userId>,<hash>"`.

### Validation Flow

1. A third party calls `/validate` with the validator string and a `key`.
2. The server splits the validator into `userId` and `hashedValue`.
3. The server looks up the user by ID to retrieve their account token.
4. The server searches the user's stored validators for one matching `hashedValue` that has not expired (`now < generatedAt + 300`).
5. If a match is found, the server recomputes the expected hash using the stored validator's window, the provided `key`, and the user's actual account token.
6. If the recomputed hash matches `hashedValue`, the validator is valid.

### Expiration and Cleanup

- Each validator expires exactly **5 minutes after it was generated**.
- A background process runs every 300 seconds and prunes expired validators from memory.
- Expired validators are also pruned when a new validator is generated for the same user.
- Validators are **in-memory only**. A server restart invalidates all pending validators.

***

## Permissions

| Permission | Description |
|------------|-------------|
| `validators:generate` | Required by sub-tokens to call `/generate_validator` |

The `/validate` endpoint requires no permission and no authentication.

This permission is included in the `full` permission group for sub-tokens.

***

## Security Considerations

- **Time-limited:** each validator expires 5 minutes after generation, limiting the window for replay attacks.
- **Key-bound:** the hash incorporates both the application key and the user's account token, so a validator cannot be reused for a different key or user.
- **No secret exposure:** the user's account token is never returned by these endpoints; it is only mixed into the hash server-side.
- **In-memory only:** validators are not persisted to disk.
- **Public validation:** the `/validate` endpoint is unauthenticated by design. The security model relies on the hash being computationally infeasible to forge without knowing the account token.
