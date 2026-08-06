# POST `/cosmetics/equip/:id`

Equip a cosmetic you own. This sets it as your active cosmetic for its type. For overlays, this also updates `sys.overlay` on your user object so the avatar server renders it.

**Authentication:** Required. **Permission:** `cosmetics:equip`.

**Path Parameter:**

| Parameter | Description |
|---|---|
| `:id` | The cosmetic's unique ID (must be one you own) |

**Example request:**

```http
POST /cosmetics/equip/cat_ears
```

**Example response (200):**

```json
{
  "message": "Cosmetic equipped successfully"
}
```

**Common errors:**

| Status | Error |
|---|---|
| `400` | `Invalid cosmetic id` |
| `403` | `You do not own this cosmetic` |
| `404` | `Cosmetic not found` |
