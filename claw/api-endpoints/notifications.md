# /notifications

## About

Returns a list of notification events for the authenticated user, such as follows, replies, and other interactions.

## Parameters

| Parameter | Description |
| --- | --- |
| auth | A required user authentication key |
| after | Optional. A number of days to look back (default 1). Only events after this period are returned |

## Endpoint

```
GET /notifications?auth=YOUR_AUTH_KEY&after=7
```

## Response

```json
[
  {
    "type": "follow",
    "id": "a1b2c3d4",
    "timestamp": 1715054321000,
    "followers": ["mist", "rm"]
  },
  {
    "type": "reply",
    "id": "e5f6a7b8",
    "timestamp": 1715054000000,
    "post_id": "abc123"
  }
]
```

Events are sorted newest first, with a maximum of 100 events stored per user.
