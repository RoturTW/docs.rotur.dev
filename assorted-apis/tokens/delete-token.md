# Delete a Sub-Token

> **Authentication:** Required (main account token only)

### DELETE `/tokens/:id`

**Description:**
Permanently delete a sub-token from your token store. Unlike revocation, this removes the token record entirely.

**Path Parameter:**
* `:id` — the sub-token ID (e.g. `st_abc123`)

**Query Parameters:**
* `auth` — your rotur user token (required, must be the main account token)

**Example:**

```http
DELETE /tokens/st_abc123?auth=YOUR_MAIN_TOKEN
```

**Response (200):**

```json
{
  "message": "Token deleted successfully",
  "id": "st_abc123"
}
```

**Error Responses:**

| Status | Condition |
|---|---|
| 403 | Authenticated with a sub-token instead of the main account token |
| 404 | Token not found |
