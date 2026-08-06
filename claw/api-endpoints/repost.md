# /repost

Reposts a post, either as a plain repost on your profile or as a quote post on the public feed.

Requires authentication, the `posts:repost` permission, and `good` account standing.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| id | Yes | The ID of the post you are reposting |
| content | No | Quote text. If you add content, the repost becomes a quote post and appears on the public feed. Without content it only shows on your profile |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/repost?id=POST_ID&content=Look+at+this"
```

## Response

Returns `201` with the new repost object. It has `is_repost: true` and includes the `original_post`.

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `You cant repost this post` | The post author has blocked you |
| 400 | `Content exceeds N character limit` | Quote text too long for your tier |
| 403 | `Cannot repost a profile-only post` | The original post is profile-only |
| 403 | `Cannot repost a repost` | The original post is itself a repost |
| 404 | `Original post not found` | No post with that ID |
