# /claim\_daily

## About

Claims the daily credit reward for the authenticated user. Returns an error if the cooldown has not elapsed.

## Parameters

| Parameter | Description |
| --- | --- |
| auth | A required user authentication key |

## Endpoint

```
GET /claim_daily?auth=YOUR_AUTH_KEY
```

## Response

```json
{
  "message": "Daily claimed!",
  "amount": 1.5,
  "new_total": 42.85
}
```

The amount varies based on subscription tier. Requires `good` account standing.
