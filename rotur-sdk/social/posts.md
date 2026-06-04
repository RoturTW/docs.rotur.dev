# Posts

Accessed via `rotur.posts`. Post creation and interaction require authentication; feeds are public.

## Create a Post

```ts
const post = await rotur.posts.create("Hello world!");
```

With options:

```ts
const post = await rotur.posts.create("Check this out", {
  attachment: "https://example.com/image.png",
  profileOnly: true,   // only shows on your profile
  os: "originOS",      // system tag
});
```

## Delete a Post

```ts
await rotur.posts.delete("post-id");
```

## Like / Unlike

```ts
await rotur.posts.like("post-id");
await rotur.posts.unlike("post-id");
```

## Reply

```ts
const reply = await rotur.posts.reply("post-id", "Nice post!");
```

## Repost

```ts
const repost = await rotur.posts.repost("post-id");
```

## Pin / Unpin

```ts
await rotur.posts.pin("post-id");
await rotur.posts.unpin("post-id");
```

## Feeds

```ts
// Public feed (no auth required)
const feed = await rotur.posts.feed(100, 0);  // limit, offset

// Following feed (auth required)
const feed = await rotur.posts.followingFeed(50);

// Top posts by likes in the last N hours
const top = await rotur.posts.top(50, 24);

// Search posts
const results = await rotur.posts.search("hello", 20);
```

## Content Limits

```ts
const limits = await rotur.posts.limits();
// { content_length: 300, content_length_premium: 600, attachment_length: 200 }
```
