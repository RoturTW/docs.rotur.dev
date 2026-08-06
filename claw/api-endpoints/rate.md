# /rate

Likes or unlikes a post.

Requires authentication and the `posts:like` permission.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| id | Yes | The ID of the post to rate |
| rating | Yes | `1` to like the post, `0` to remove your like |

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/rate?id=POST_ID&rating=1"
```

## Response

```json
{
  "message": "Post rated successfully",
  "likes": ["user_id_1", "user_id_2"]
}
```

{% hint style="info" %}
The `likes` array here contains user IDs. Post objects returned by the feed endpoints resolve likes to usernames.
{% endhint %}

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Rating must be 1 (like) or 0 (unlike)` | Invalid `rating` value |
| 400 | `You cant like this post` | The post author has blocked you |
| 404 | `Post not found` | No post with that ID |
