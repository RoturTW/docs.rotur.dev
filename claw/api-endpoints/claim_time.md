# GET `/claim_time`

Returns how long until you can claim your next daily credit reward with [`/claim_daily`](claim_daily.md).

**Auth:** Required. Sub-tokens need `credits:daily`.

### Example

```http
GET /claim_time
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "wait_time": 43200
}
```

`wait_time` is in seconds. `0` means you can claim now.

### Errors

| Status | When |
| --- | --- |
| `400` | `No daily claim found`. You have never claimed, so you can claim right away |
