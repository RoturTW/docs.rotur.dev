# GET `/cosmetics/gifts/mine`

List the cosmetic gifts you have sent and received.

**Auth:** Required. Sub-tokens need `cosmetics:view`.

### Example

```http
GET /cosmetics/gifts/mine
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "received": [
    {
      "id": "a1b2c3d4...",
      "cosmetic_id": "maga",
      "cosmetic_name": "Maga",
      "from": "mist",
      "to": "allucat1000",
      "note": "Enjoy",
      "amount": 50,
      "created_at": 1715100000000,
      "claimed_at": 1715100000000
    }
  ],
  "sent": []
}
```

| Field | Description |
| --- | --- |
| `id` | Gift ID |
| `cosmetic_id` | ID of the gifted cosmetic |
| `cosmetic_name` | Display name of the cosmetic, or its ID if it is no longer in the catalog |
| `from` / `to` | Sender and recipient usernames |
| `note` | The sender's note, or an empty string |
| `amount` | The list price the sender paid, excluding tax |
| `created_at` | When the gift was sent (Unix ms) |
| `claimed_at` | When it was added to the recipient's inventory (Unix ms). Omitted if unclaimed |
