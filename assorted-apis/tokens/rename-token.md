# Rename a Sub-Token

Change just the name of a sub-token.

> **Authentication:** Required (main account token only)

### POST `/tokens/:id/rename`

**Path Parameter:**
* `:id`: the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `Authorization`: send `Bearer <token>` via the `Authorization` header (preferred). `auth` query parameter is accepted as legacy fallback.

**Request Body (JSON):**

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | New name (1-50 characters) |

**Example:**

```http
POST /tokens/st_abc123/rename
Content-Type: application/json

{
  "name": "My App - Renamed"
}
```

**Response (200):**

```json
{
  "message": "Token renamed successfully",
  "id": "st_abc123",
  "name": "My App - Renamed"
}
```

**Error Responses:**

| Status | Condition |
|---|---|
| 400 | `name` is empty or longer than 50 characters |
| 403 | Authenticated with a sub-token instead of the main account token |
| 404 | Token not found |
