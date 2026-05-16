# POST `/cosmetics/equip/:id`

Equip a cosmetic you own. This sets it as your active cosmetic for its type. For overlays, this also updates `sys.overlay` on your user object so the avatar server renders it.

**Authentication:** Required.

**Path Parameter:**

| Parameter | Description |
|---|---|
| `:id` | The cosmetic's unique ID (must be one you own) |

**Example:**

```http
POST /cosmetics/equip/cat_ears?auth=YOUR_TOKEN
```

**Response (200):**

```json
{
  "message": "Cosmetic equipped successfully",
  "active_cosmetics": {
    "overlay": "cat_ears"
  }
}
```

**Error Responses:**

| Status | Error |
|---|---|
| `403` | `You do not own this cosmetic` |
| `404` | `Cosmetic not found` |
