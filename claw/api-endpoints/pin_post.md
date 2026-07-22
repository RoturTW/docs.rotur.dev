# /pin\_post

Pins one of your posts to the top of your profile.

Requires authentication and the `posts:manage` permission. How many posts you can pin depends on your subscription: 1 (Free/Lite), 3 (Plus), 5 (Pro and up).

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key |
| id | Yes | The ID of the post to pin |

## Example

```bash
curl "https://api.rotur.dev/pin_post?auth=YOUR_AUTH_KEY&id=POST_ID"
```

## Response

```json
{
  "message": "Post pinned successfully"
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 403 | `You can only pin your own posts` | The post belongs to someone else |
| 403 | `Pin limit reached (N). ...` | You already have the maximum number of pins for your tier |
| 404 | `Post not found` | No post with that ID |
