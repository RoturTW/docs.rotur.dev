# Delete a token

Permanently remove a sub-token from your account.

## DELETE `/tokens/:id`

Deletes the sub-token record. If the token was still active it stops working immediately. Unlike [revoking](revoke-token.md), nothing is kept.

**Auth:** Required. Main token only.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The sub-token ID, for example `st_abc123` |

### Example

```http
DELETE /tokens/st_abc123
Authorization: Bearer <main token>
```

**Response `200`:**

```json
{
  "message": "Token deleted successfully",
  "id": "st_abc123"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | You authenticated with a sub-token |
| `404` | No sub-token with this ID exists on the account |
