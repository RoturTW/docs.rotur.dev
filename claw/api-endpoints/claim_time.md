# /claim\_time

## About

Returns the time remaining until the authenticated user can claim their next daily credit reward.

## Parameters

| Parameter | Description |
| --- | --- |
| auth | A required user authentication key |

## Endpoint

```
GET /claim_time?auth=YOUR_AUTH_KEY
```

## Response

```json
{
  "can_claim": false,
  "time_remaining_ms": 43200000
}
```
