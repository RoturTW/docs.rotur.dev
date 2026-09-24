# GET `/feed`

Returns public posts, newest first. Profile-only posts are left out.

**Auth:** Optional. Send your main account token to get `poll.voted` on posts with polls; sub-tokens are ignored here.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `limit` | query | integer | No | Number of posts to return, 1–100. Default 100 |
| `offset` | query | integer | No | Number of posts to skip from the newest. Default 0 |

### Example

```http
GET /feed?limit=2&offset=0
```

**Response `200`:**

```json
[
  {
    "id": "abc123",
    "content": "Hello Claw",
    "user": "mist",
    "timestamp": 1715054321000,
    "likes": ["rm"],
    "replies": [
      {
        "id": "def456",
        "content": "Hi",
        "user": "rm",
        "timestamp": 1715054400000
      }
    ],
    "views": 12
  }
]
```

### Post object

| Field | Type | Description |
| --- | --- | --- |
| `id` | string | Post ID |
| `content` | string | Post text |
| `user` | string | Author's username |
| `timestamp` | integer | When the post was published, in Unix milliseconds |
| `likes` | string[] | Usernames of users who liked the post |
| `replies` | object[] | Replies, each with `id`, `content`, `user` and `timestamp` |
| `views` | integer | Number of users who have viewed the post |
| `attachment` | string | First attachment URL |
| `attachments` | string[] | All attachment URLs |
| `os` | string | System the post was tagged with |
| `profile_only` | boolean | `true` if the post only shows on the author's profile |
| `pinned` | boolean | `true` if the author pinned the post |
| `is_repost` | boolean | `true` for reposts and quote posts |
| `original_post` | object | The reposted post, same shape |
| `edited_at` | integer | When the post was last edited, in Unix milliseconds |
| `premium` | boolean | `true` if the author has Plus or higher |
| `tier` | string | Author's paid subscription tier |
| `group_tag` | string | Author's group tag |
| `poll` | object | Poll with `options` (each `text` and `count`), `total`, and `voted` (the option index you voted for) |

Fields other than `id`, `content`, `user` and `timestamp` are left out when they are empty, `false` or zero.
