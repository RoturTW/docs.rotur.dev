# /vote\_poll

Votes in a post's poll. Voting again changes your vote.

Requires authentication.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |
| id | Yes | The ID of the post with the poll |
| option | Yes | The option index to vote for, starting at 0 |

## Example

```bash
curl "https://api.rotur.dev/vote_poll?auth=YOUR_AUTH_KEY&id=POST_ID&option=1"
```

## Response

```json
{
  "poll": {
    "options": [
      { "text": "Yes", "count": 4 },
      { "text": "No", "count": 7 }
    ],
    "total": 11,
    "voted": 1
  }
}
```

`voted` is the option index you voted for.

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Post ID and option are required` | Missing parameter |
| 400 | `Invalid option` | `option` is not a number |
| 400 | `Option out of range` | `option` does not match a poll option |
| 404 | `Poll not found` | The post does not exist or has no poll |
