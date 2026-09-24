# POST `/cosmetics/equip/:id`

Equip a cosmetic you own. It replaces whatever you had equipped of the same type. Equipping an overlay also sets `sys.overlay` on your account, which the avatar server uses to render it.

**Auth:** Required. Sub-tokens need `cosmetics:equip`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The ID of a cosmetic you own. Use `custom_overlay` or `custom_background` to equip your own upload |

### Example

```http
POST /cosmetics/equip/cat_ears
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Cosmetic equipped successfully"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Invalid cosmetic id` |
| `403` | `You do not own this cosmetic` |
| `404` | `Cosmetic not found`. This includes `custom_overlay` and `custom_background` when you have no upload for that type or no longer have the perk |
