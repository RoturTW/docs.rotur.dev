# Validators

> **Authentication:**
> All routes use the **GET** method.
> All required parameters must be passed as **URL query parameters** (after `?` in the URL).
>
> **The base url for this api is:**
> https://social.rotur.dev/

Validators are a time-limited, hash-based token verification system. They allow a third party to confirm that a user owns a particular account and holds a specific key, without exposing the user's secret key or password.

A typical flow: a user generates a validator on Rotur, gives it to a third-party service, and that service calls validate to confirm the user's identity — receiving their username and ID back, without ever seeing the user's password or key.

***

### GET `/generate_validator`

**Description:**
Generate a validator string for the authenticated user. The validator encodes a SHA-256 hash that binds the user's ID, their key, an auth key, and the current time window. The validator is valid for exactly **5 minutes** from the moment it is generated.

**Query Parameters:**
* `auth` — your rotur user token (required)
* `key` — the key value to bind into the validator hash (required)

**Response:**

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

**Example:**

```http
GET /generate_validator?auth=YOUR_TOKEN&key=myAppKey
```

***

### GET `/validate`

**Description:**
Validate a previously generated validator against a key. This endpoint is **unauthenticated** — it is designed to be called by third-party services that need to verify a user's identity.

**Query Parameters:**
* `v` — the full validator string (`userId,hashedValue`) (required)
* `key` — the key to verify against the validator (required)

**Response (valid):**

```json
{
  "valid": true,
  "username": "example_user",
  "id": "abc123def456"
}
```

**Response (invalid or expired):**

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
| 404 | User ID in validator does not exist |

**Example:**

```http
GET /validate?v=abc123def456,a1b2c3d4e5f6...&key=myAppKey
```

***

## How It Works

### Time Windows

Validators use a **5-minute lifetime** (300 seconds). The hash is computed against a rounded window boundary, but the validator remains valid for exactly 5 minutes from the moment it was generated:

```
windowStart = floor(timestamp / 300) × 300
expiresAt   = generatedAt + 300
```

This means a validator always has a full 5-minute validity period regardless of when during a window it was created.

### Hash Computation

The validator hash is computed as:

```
SHA-256(key + authKey + windowStartTimestamp)
```

Where:
- `key` — the application-specific key passed by the user
- `authKey` — the user's authentication token (used server-side)
- `windowStartTimestamp` — the Unix timestamp rounded down to the current 300-second window, as a decimal string

The result is a 64-character lowercase hex string.

### Generation Flow

1. The authenticated user calls `/generate_validator` with `key` and `auth`.
2. The server computes `windowStart = floor(now / 300) × 300`.
3. The server computes `hash = SHA-256(key + auth + windowStart)`.
4. The hash is stored in memory alongside its creation timestamp, keyed by user ID.
5. The response returns `"<userId>,<hash>"`.

### Validation Flow

1. A third party calls `/validate` with the validator string and a `key`.
2. The server splits the validator into `userId` and `hashedValue`.
3. The server looks up the user by ID to retrieve their secret key.
4. The server searches the user's stored validators for one matching `hashedValue` that has not expired (`now < generatedAt + 300`).
5. If a match is found, the server recomputes the expected hash using the stored validator's `windowStart`, the provided `key`, and the **user's actual secret key**.
6. If the recomputed hash matches `hashedValue`, the validator is valid.

### Expiration & Cleanup

- Each validator expires exactly **5 minutes after it was generated**.
- A background process runs every 300 seconds and prunes expired validators from memory.
- Expired validators are also pruned lazily when a new validator is generated for the same user.
- Validators are **in-memory only** — a server restart invalidates all pending validators.

***

## Permissions

| Permission              | String                 | Description                                |
|-------------------------|------------------------|--------------------------------------------|
| `PermGenerateValidator` | `validators:generate`  | Required to call `/generate_validator`     |

The `/validate` endpoint requires **no permission** — it is publicly accessible.

This permission is included in the `full` permission group for sub-tokens.

***

## Security Considerations

- **Time-limited:** Each validator expires exactly 5 minutes after generation, limiting the window for replay attacks.
- **Key-bound:** The hash incorporates both the application key and the user's auth key, so a validator cannot be reused for a different key or user.
- **No secret exposure:** The user's auth key is never returned in any API response; it is only mixed into the hash server-side.
- **In-memory only:** Validators are not persisted to disk. A server restart invalidates all pending validators.
- **Public validation:** The `/validate` endpoint is unauthenticated by design. The security model relies on the hash being computationally infeasible to forge without knowing the auth key.
