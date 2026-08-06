# /view

Marks one or more posts as viewed by you and returns their view counts. Each user only counts once per post.

Requires authentication.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| auth | Yes | Your authentication key. Use the `Authorization` header with `Bearer <token>` (preferred). The `auth` query parameter is still accepted as fallback. |
| id | Yes* | The ID of a single post to view |
| ids | No* | A comma-separated list of post IDs to view in one request |

*Use either `id` or `ids`.

## Example

```bash
curl -H "Authorization: Bearer YOUR_AUTH_KEY" "https://api.rotur.dev/view?id=POST_ID"
```

## Response

Single post:

```json
{
  "views": 13
}
```

Batch (`ids=a,b,c`), keyed by post ID. Unknown IDs are silently skipped:

```json
{
  "views": {
    "a": 13,
    "b": 4
  }
}
```

## Common errors

| Status | Error | Cause |
| --- | --- | --- |
| 400 | `Post ID is required` | Neither `id` nor `ids` given |
| 404 | `Post not found` | Single `id` does not exist |
