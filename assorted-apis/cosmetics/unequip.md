# POST `/cosmetics/unequip`

Remove your active cosmetic of a given type. For overlays, this also clears `sys.overlay` on your user object so the avatar server stops rendering the overlay.

**Authentication:** Required.

**Query Parameters:**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `type` | string | Yes | The cosmetic type to unequip (e.g. `overlay`) |

**Example:**

```http
POST /cosmetics/unequip?type=overlay&auth=YOUR_TOKEN
```

**Response (200):**

```json
{
  "message": "Cosmetic removed successfully",
  "active_cosmetics": {}
}
```

**Error Responses:**

| Status | Error |
|---|---|
| `400` | `type query parameter is required (e.g. overlay)` |
| `400` | `No active cosmetic of that type to remove` |
