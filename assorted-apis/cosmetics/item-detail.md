# GET `/cosmetics/items/:id`

Get one catalog cosmetic by its ID.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `id` | path | string | Yes | The cosmetic ID |

### Example

```http
GET /cosmetics/items/cat_ears
```

**Response `200`:**

```json
{
  "id": "cat_ears",
  "cosmetic_type": "overlay",
  "name": "Cat Ears",
  "description": "Cute cat ear overlay for your avatar",
  "image_url": "",
  "pricing_type": "free",
  "price": 0,
  "creator": "mist",
  "creator_pct": 80,
  "featured": true,
  "purchases": 42,
  "created_at": 1715000000000
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `Invalid cosmetic id`: the ID is empty, longer than 50 characters, or has characters other than letters, numbers, `-` and `_` |
| `404` | `Cosmetic not found` |
