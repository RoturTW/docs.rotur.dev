# /unbookmark

Removes a post from your bookmarks.

Requires authentication. Always succeeds, even if the post was not bookmarked.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| id | Yes | The ID of the post to remove |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/unbookmark?id=POST_ID"
```

## Response

```json
{
  "message": "Removed"
}
```
