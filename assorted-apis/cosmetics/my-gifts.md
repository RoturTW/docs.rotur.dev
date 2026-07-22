# GET `/cosmetics/gifts/mine`

List cosmetic gifts you have sent and received.

**Authentication:** Required. **Permission:** `cosmetics:view`.

**Example request:**

```http
GET /cosmetics/gifts/mine?auth=YOUR_TOKEN
```

**Example response (200):**

```json
{
  "received": [
    {
      "id": "a1b2c3d4...",
      "cosmetic_id": "maga",
      "cosmetic_name": "Maga",
      "from": "mist",
      "to": "allucat1000",
      "note": "Enjoy!",
      "amount": 50,
      "created_at": 1715100000000,
      "claimed_at": 1715100000000
    }
  ],
  "sent": []
}
```

| Field | Description |
|---|---|
| `id` | Unique gift ID |
| `cosmetic_id` | ID of the gifted cosmetic |
| `cosmetic_name` | Display name of the cosmetic |
| `from` / `to` | Sender and recipient usernames |
| `note` | The sender's note, if any |
| `amount` | The list price paid by the sender (excluding tax) |
| `created_at` | When the gift was sent (Unix ms) |
| `claimed_at` | When it was claimed (Unix ms). Omitted if unclaimed. |
