# Revoke a Sub-Token

Revoke a sub-token, making it immediately unusable.

> **Authentication:** Required (main account token only)

### POST `/tokens/:id/revoke`

A revoked token cannot be un-revoked. If you need the access back, create a new token.

**Path Parameter:**
* `:id`: the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Example:**

```http
POST /tokens/st_abc123/revoke
```

**Response (200):**

```json
{
  "message": "Token revoked successfully",
  "id": "st_abc123"
}
```

**Error Responses:**

| Status | Condition |
|---|---|
| 400 | Token is already revoked |
| 403 | Authenticated with a sub-token instead of the main account token |
| 404 | Token not found |
