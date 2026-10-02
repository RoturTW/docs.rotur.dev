# Posts

`rotur.posts` creates posts and reads feeds. Reading public feeds, single posts, and limits needs no token; everything else does.

The end of this page also covers `rotur.reports`, for reporting posts, replies and users, and `rotur.emojis`, for custom emojis.

Posts are returned as `NetPost`: `id`, `content`, `user`, `timestamp`, `profile_only`, `is_repost`, and optional `attachment`, `attachments`, `os`, `replies`, `likes`, `pinned`, `original_post`, `edited_at`, `premium`, `tier`, `group_tag`, `views`, and `poll`.

## rotur.posts.create(content, options?)

Creates a post, or schedules one when you pass `scheduledFor`.

**Auth:** Required. Sub-tokens need `posts:create`.

| Option | Type | Description |
| --- | --- | --- |
| `attachment` | string | One attachment URL |
| `attachments` | string[] | Several attachment URLs |
| `profileOnly` | boolean | Show the post only on your profile |
| `os` | string | System tag |
| `poll` | string[] | Poll options |
| `scheduledFor` | number | Timestamp to publish the post at |

```ts
const post = await rotur.posts.create("Hello world");

await rotur.posts.create("Check this out", {
  attachment: "https://example.com/image.png",
  profileOnly: true,
  os: "originOS",
});

await rotur.posts.create("Tabs or spaces?", { poll: ["Tabs", "Spaces"] });
```

**Returns:** the new `NetPost`, or `{ scheduled: true, publish_at, id }` for a scheduled post.

## rotur.posts.get(id)

Gets one post.

**Auth:** None.

```ts
const post = await rotur.posts.get("post-id");
```

**Returns:** `NetPost`

## rotur.posts.edit(id, content)

Replaces the text of your post.

**Auth:** Required. Sub-tokens need `posts:manage`.

```ts
const { edited_at } = await rotur.posts.edit("post-id", "Fixed a typo");
```

**Returns:** `{ message, edited_at }`

## rotur.posts.delete(id)

Deletes your post.

**Auth:** Required. Sub-tokens need `posts:delete`.

```ts
await rotur.posts.delete("post-id");
```

**Returns:** `{ message }`

## rotur.posts.reply(id, content)

Replies to a post.

**Auth:** Required. Sub-tokens need `posts:reply`.

```ts
const reply = await rotur.posts.reply("post-id", "Nice post");
```

**Returns:** `{ id, content, user, timestamp }`

## rotur.posts.repost(id)

Reposts a post to your profile.

**Auth:** Required. Sub-tokens need `posts:repost`.

```ts
const repost = await rotur.posts.repost("post-id");
```

**Returns:** the new `NetPost`.

## rotur.posts.like(id)

Likes a post.

**Auth:** Required. Sub-tokens need `posts:like`.

```ts
const { likes } = await rotur.posts.like("post-id");
```

**Returns:** `{ message, likes: string[] }`

## rotur.posts.unlike(id)

Removes your like from a post.

**Auth:** Required. Sub-tokens need `posts:like`.

```ts
await rotur.posts.unlike("post-id");
```

**Returns:** `{ message, likes: string[] }`

## rotur.posts.pin(id)

Pins your post to your profile.

**Auth:** Required. Sub-tokens need `posts:manage`.

```ts
await rotur.posts.pin("post-id");
```

**Returns:** `{ message }`

## rotur.posts.unpin(id)

Unpins your post.

**Auth:** Required. Sub-tokens need `posts:manage`.

```ts
await rotur.posts.unpin("post-id");
```

**Returns:** `{ message }`

## rotur.posts.vote(id, option)

Votes in a post's poll. `option` is the index of the option.

**Auth:** Required. Sub-tokens need `posts:vote`.

```ts
const { poll } = await rotur.posts.vote("post-id", 0);
```

**Returns:** `{ poll: { options: [{ text, count }], total, voted? } }`

## rotur.posts.view(id)

Records a view of a post.

**Auth:** Required. Sub-tokens need `posts:view`.

```ts
const { views } = await rotur.posts.view("post-id");
```

**Returns:** `{ views: number }`

## rotur.posts.viewMany(ids)

Records views of several posts in one request. An empty array returns `{ views: {} }` without a request.

**Auth:** Required. Sub-tokens need `posts:view`.

```ts
const { views } = await rotur.posts.viewMany(["post-1", "post-2"]);
```

**Returns:** `{ views: Record<string, number> }`, keyed by post ID.

## rotur.posts.bookmark(id)

Bookmarks (saves) a post.

**Auth:** Required. Sub-tokens need `posts:bookmark`.

```ts
await rotur.posts.bookmark("post-id");
```

**Returns:** `{ message }`

## rotur.posts.unbookmark(id)

Removes a bookmark.

**Auth:** Required. Sub-tokens need `posts:bookmark`.

```ts
await rotur.posts.unbookmark("post-id");
```

**Returns:** `{ message }`

## rotur.posts.bookmarks()

Lists the posts you have bookmarked.

**Auth:** Required. Sub-tokens need `posts:view`.

```ts
const saved = await rotur.posts.bookmarks();
```

**Returns:** `NetPost[]`

## rotur.posts.scheduled()

Lists your scheduled posts that have not been published yet.

**Auth:** Required. Sub-tokens need `posts:view`.

```ts
const pending = await rotur.posts.scheduled();
```

**Returns:** `NetPost[]`

## rotur.posts.feed(limit?, offset?)

Gets the public feed.

**Auth:** None.

| Name | Type | Default |
| --- | --- | --- |
| `limit` | number | `100` |
| `offset` | number | `0` |

```ts
const feed = await rotur.posts.feed(100, 0);
```

**Returns:** `NetPost[]`

## rotur.posts.followingFeed(limit?)

Gets posts from users you follow. `limit` defaults to `100`.

**Auth:** Required. Sub-tokens need `posts:view`.

```ts
const feed = await rotur.posts.followingFeed(50);
```

**Returns:** `NetPost[]`

## rotur.posts.top(limit?, timePeriod?)

Gets the most-liked posts from the last `timePeriod` hours.

**Auth:** None.

| Name | Type | Default |
| --- | --- | --- |
| `limit` | number | `50` |
| `timePeriod` | number | `24` (hours) |

```ts
const top = await rotur.posts.top(50, 24);
```

**Returns:** `NetPost[]`

## rotur.posts.search(query, limit?)

Searches posts. `limit` defaults to `20`.

**Auth:** None.

```ts
const results = await rotur.posts.search("hello", 20);
```

**Returns:** `NetPost[]`

## rotur.posts.limits()

Gets the post length limits.

**Auth:** None.

```ts
const limits = await rotur.posts.limits();
// { content_length: 300, content_length_premium: 600, attachment_length: 200 }
```

**Returns:** `{ content_length, content_length_premium, attachment_length }`

## rotur.reports.submit(targetType, targetId, reason)

Reports a post, reply or user to Rotur staff. You can't report yourself or report the same thing twice.

**Auth:** Required. Sub-tokens need `reports:create`.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `targetType` | `"post"`, `"reply"`, or `"user"` | Yes | What you are reporting |
| `targetId` | string | Yes | The post ID, reply ID, or username |
| `reason` | string | Yes | Why you are reporting it, up to 1000 characters |

```ts
const { report_id } = await rotur.reports.submit("post", "post-id", "Spam");
```

**Returns:** `{ success, report_id }`

## rotur.emojis.mine()

`rotur.emojis` manages custom emojis. Uploading and saving emojis needs a Plus or higher subscription. For the limits per tier, see [Emojis](../../api-reference/emojis.md).

Lists the emojis you uploaded and the ones you saved from other users.

**Auth:** Required. Sub-tokens need `emojis:view`.

```ts
const { uploaded, added } = await rotur.emojis.mine();
```

**Returns:** `{ uploaded, added }`. Each emoji is `{ id, hash, name, owner, url }`.

## rotur.emojis.upload(name, image)

Uploads a new emoji. `image` is a base64 data URI, up to 5 MB. `name` can be up to 80 characters.

**Auth:** Required. Sub-tokens need `emojis:manage`.

```ts
const emoji = await rotur.emojis.upload("wave", "data:image/png;base64,...");
```

**Returns:** `{ status: "uploaded", id, hash, name, content_type, url }`

## rotur.emojis.add(id, name?)

Saves another user's emoji to your collection. `name` defaults to the emoji's own name.

**Auth:** Required. Sub-tokens need `emojis:manage`.

```ts
await rotur.emojis.add(12345, "party");
```

**Returns:** `{ status: "added", id, hash, name, url }`

## rotur.emojis.unsave(id)

Removes a saved emoji from your collection. It doesn't work on emojis you uploaded; use `delete()` for those.

**Auth:** Required. Sub-tokens need `emojis:manage`.

```ts
await rotur.emojis.unsave(12345);
```

**Returns:** `{ status: "unsaved", id }`

## rotur.emojis.delete(id)

Deletes an emoji you uploaded. It is also removed from everyone who saved it.

**Auth:** Required. Sub-tokens need `emojis:manage`.

```ts
const { users_updated } = await rotur.emojis.delete(12345);
```

**Returns:** `{ status: "deleted", id, hash, users_updated }`

## rotur.emojis.get(id)

Downloads an emoji image. Throws an `ApiError` if the emoji does not exist.

**Auth:** None.

```ts
const blob = await rotur.emojis.get(12345);
```

**Returns:** `Blob`

## rotur.emojis.url(id)

Builds an emoji's image URL without making a request.

**Auth:** None.

```ts
rotur.emojis.url(12345);
// "https://api.rotur.dev/v2/emojis/12345"
```

**Returns:** `string`
