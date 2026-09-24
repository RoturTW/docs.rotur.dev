# Rename a token

Change only the name of a sub-token.

## POST `/tokens/:id/rename`

Sets a new name. This works on revoked tokens too.

**Auth:** Required. Main token only.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The sub-token ID, for example `st_abc123` |
| `name` | body | string | Yes | New name, 1–50 characters |

### Example

```http
POST /tokens/st_abc123/rename
Authorization: Bearer <main token>
Content-Type: application/json

{
  "name": "My App - Renamed"
}
```

**Response `200`:**

```json
{
  "message": "Token renamed successfully",
  "id": "st_abc123",
  "name": "My App - Renamed"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The body is not valid JSON |
| `400` | `name` is empty or longer than 50 characters |
| `403` | You authenticated with a sub-token |
| `404` | No sub-token with this ID exists on the account |
