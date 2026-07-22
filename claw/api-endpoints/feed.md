# /feed

The feed is the main display of Claw. It returns public posts, newest first.

Authentication is optional. If you pass your token, poll data includes which option you voted for.

## Parameters

| Parameter | Required | Description |
| --------- | -------- | ----------- |
| limit | No | How many posts to return, between 1 and 100. Default 100 |
| offset | No | How many posts to skip from the top. Default 0 |

## Example

```bash
curl "https://api.rotur.dev/feed?limit=2&offset=0"
```

## Response

Returns an array of post objects:

```json
[
  {
    "id": "abc123",
    "content": "Hello Claw!",
    "user": "mist",
    "timestamp": 1715054321000,
    "likes": ["rm"],
    "replies": [
      {
        "id": "def456",
        "content": "Hi!",
        "user": "rm",
        "timestamp": 1715054400000
      }
    ],
    "views": 12
  }
]
```

Posts can also include `attachment`, `attachments`, `os`, `pinned`, `is_repost`, `original_post`, `edited_at`, `premium`, `tier`, `group_tag`, and `poll` fields when they apply.
