# Rename a Sub-Token

> **Authentication:** Required (main account token only)

### POST `/tokens/:id/rename`

**Description:**
Rename a sub-token. This is a convenience endpoint for changing just the name field.

**Path Parameter:**
* `:id` — the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `auth` — your rotur user token (required, must be the main account token)

**Request Body (JSON):**

| Field | Type | Required | Description |
|---|---|---|---|
| `name` | string | Yes | New name (1–50 characters) |

**Example:**

```http
POST /tokens/st_abc123/rename?auth=YOUR_MAIN_TOKEN
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
