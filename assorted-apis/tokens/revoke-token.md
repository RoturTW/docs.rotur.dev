# Revoke a token

Revoke a sub-token so it stops working immediately.

## POST `/tokens/:id/revoke`

Marks the sub-token as revoked and records `revoked_at`. The record stays on your account; use [Delete a token](delete-token.md) to remove it.

**Auth:** Required. Main token only.

{% hint style="warning" %}
Revoking cannot be undone. To give the app access again, create a new token.
{% endhint %}

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The sub-token ID, for example `st_abc123` |

### Example

```http
POST /tokens/st_abc123/revoke
Authorization: Bearer <main token>
```

**Response `200`:**

```json
{
  "message": "Token revoked successfully",
  "id": "st_abc123"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | The token is already revoked |
| `403` | You authenticated with a sub-token |
| `404` | No sub-token with this ID exists on the account |
