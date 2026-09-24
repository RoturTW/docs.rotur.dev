# GET `/claim_daily`

Claims your daily credit reward. You can claim once every 24 hours.

**Auth:** Required. Sub-tokens need `credits:daily`. Your account needs `good` standing.

You receive 1 credit on Free and Lite, 2 on Plus, and 3 on Pro and Max.

### Example

```http
GET /claim_daily
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "message": "Daily claim successful"
}
```

### Errors

| Status | When |
| --- | --- |
| `429` | `Daily claim already made`. The body also has `wait_time` (seconds until you can claim) and `wait_hours` (the same in hours, as a string) |
| `500` | `Could not record daily claim` |

```json
{
  "error": "Daily claim already made",
  "wait_time": 43200,
  "wait_hours": "12"
}
```
