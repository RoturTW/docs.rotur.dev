# /get\_post

Fetches a single post by its ID.

Authentication is optional. If you pass your token and the post has a poll, the response includes which option you voted for.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| id | Yes | The ID of the post to fetch |
| auth | No | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |

## Example

```bash
curl "https://api.rotur.dev/get_post?id=POST_ID"
```

## Response

Returns the post object, same shape as posts in [/feed](feed.md).

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Post ID is required` | Missing `id` parameter |
| 404 | `Post not found` | No post with that ID |
