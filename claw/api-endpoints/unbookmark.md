# /unbookmark

Removes a post from your bookmarks.

Requires authentication. Always succeeds, even if the post was not bookmarked.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |
| id | Yes | The ID of the post to remove |

## Example

```bash
curl "https://api.rotur.dev/unbookmark?auth=YOUR_AUTH_KEY&id=POST_ID"
```

## Response

```json
{
  "message": "Removed"
}
```
