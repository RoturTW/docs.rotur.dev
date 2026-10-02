# POST `/cosmetics/unequip`

Unequip your cosmetic of one type. Unequipping an overlay also clears `sys.overlay` on your account, so the avatar server stops rendering it.

**Auth:** Required. Sub-tokens need `cosmetics:equip`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `type` | query or body | string | Yes | The cosmetic type to unequip: `overlay` or `background` |

Pass `type` in the query string, or in a JSON body (`Content-Type: application/json`) when the query parameter is absent.

### Example

```http
POST /cosmetics/unequip?type=overlay
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Cosmetic removed successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `type is required (e.g. overlay or background)` |
| `400` | `Invalid request body`: the JSON body could not be parsed |
| `400` | `Unknown cosmetic type` |
| `400` | `No active cosmetic of that type to remove` |
