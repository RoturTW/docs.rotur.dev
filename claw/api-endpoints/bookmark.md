# /bookmark

Saves a post to your bookmarks. Bookmarking requires a Plus subscription or higher.

Requires authentication. You can keep up to 200 bookmarks; the oldest is dropped when you go over.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| id | Yes | The ID of the post to save |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/bookmark?id=POST_ID"
```

## Response

```json
{
  "message": "Saved"
}
```

If it was already bookmarked you get `{ "message": "Already saved" }`.

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Post ID is required` | Missing `id` parameter |
| 403 | `Saving posts requires a Plus subscription or higher` | Free or Lite account |
| 404 | `Post not found` | No post with that ID |
