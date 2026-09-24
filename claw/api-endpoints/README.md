# API endpoints

Claw's endpoints live on the main Rotur API server. Use them to read and write posts, follow users, and manage the account features Claw clients show (friends, files, items, gifts, badges and daily credits).

> **Base URL:** `https://api.rotur.dev`
>
> **Auth:** Send your account token in the `Authorization` header, as `Bearer <token>` or the bare token. The legacy `auth` query parameter (`?auth=<token>`) and the `claw_session` cookie (trusted browser origins only) also work. Each endpoint's **Auth** line says whether it needs a token.

Most Claw endpoints are `GET` requests that take their input as query parameters, including the ones that change data.

## Concepts

### Authentication errors

Endpoints that require a token return `403` with an `error` field when:

| Error | When |
| --- | --- |
| `auth key is required` | No token was sent |
| `Invalid authentication key` | The token does not match an account or sub-token |
| `User is banned` | The account is banned |
| `Email address not verified` | The account's email is not verified |
| `Terms-Of-Service are not accepted or outdated` | The account has not accepted the current Terms of Service |

### Token permissions

Your main account token can call every endpoint. A sub-token can only call an endpoint if it holds that endpoint's permission (for example `posts:create` for [`/post`](post.md)); otherwise you get `403` with `Token lacks permission: <permission>`. See [permissions](../../assorted-apis/tokens/permissions.md) for the full list.

### Account standing and restrictions

Some endpoints need your account to be in `good` standing, or at least `warning`. If it is not, you get `403` with `Your account standing does not allow this action. Current: <standing>`.

Moderators can also block individual features (posting, social actions, items, gifts) on an account. A blocked feature returns `403` with an error ending in `is blocked for this account`, plus `feature`, and `reason` and `expires_at` when set.

### Subscription tiers

Several limits depend on your subscription tier (Free, Lite, Plus, Pro or Max):

| Limit | Free | Lite | Plus | Pro | Max |
| --- | --- | --- | --- | --- | --- |
| Post, reply and edit length (characters) | 300 | 400 | 600 | 800 | 1000 |
| Attachments per post | 1 | 1 | 2 | 4 | 4 |
| Pinned posts | 1 | 1 | 3 | 5 | 5 |
| [`/following_feed`](following_feed.md) max `limit` | 100 | 100 | 200 | 200 | 200 |
| Editing, polls, scheduling, bookmarks | No | No | Yes | Yes | Yes |

Length limits count bytes, so characters outside ASCII use more than one.

### Rate limits

Limits are counted per account when you send a valid token, and per IP address otherwise. Requests with a valid token get the higher limit.

| Bucket | Without a token | With a token |
| --- | --- | --- |
| default | 100 per minute | 300 per minute |
| profile | 30 per minute | 120 per minute |
| follow | 20 per minute | 60 per minute |
| search | 20 per minute | 60 per minute |

[`/post`](post.md) also has its own limit of 5 posts per minute. Over a limit you get `429` with `X-RateLimit-Limit`, `X-RateLimit-Remaining` and `X-RateLimit-Reset` headers.

### Post objects

Endpoints that return posts use the shape shown on [`/feed`](feed.md).

## Endpoints

### Posts

| Endpoint | Description |
| --- | --- |
| [GET `/feed`](feed.md) | Public feed, newest first |
| [GET `/get_post`](get_post.md) | One post by ID |
| [GET `/post`](post.md) | Create a post |
| [GET `/edit_post`](edit_post.md) | Edit your post |
| [GET `/delete`](delete.md) | Delete your post |
| [GET `/reply`](reply.md) | Reply to a post |
| [GET `/repost`](repost.md) | Repost or quote a post |
| [GET `/rate`](rate.md) | Like or unlike a post |
| [GET `/view`](view.md) | Record views and get view counts |
| [GET `/vote_poll`](vote_poll.md) | Vote in a poll |
| [GET `/pin_post`](pin_post.md) | Pin a post to your profile |
| [GET `/unpin_post`](unpin_post.md) | Unpin a post |
| [GET `/bookmark`](bookmark.md) | Bookmark a post |
| [GET `/unbookmark`](unbookmark.md) | Remove a bookmark |
| [GET `/bookmarks`](bookmarks.md) | List your bookmarks |
| [GET `/scheduled`](scheduled.md) | List your scheduled posts |
| [GET `/following_feed`](following_feed.md) | Posts from users you follow |
| [GET `/top_posts`](top_posts.md) | Most-liked recent posts |
| [GET `/search_posts`](search_posts.md) | Search post text |
| [GET `/limits`](limits.md) | Base content limits |

### Users and following

| Endpoint | Description |
| --- | --- |
| [GET `/profile`](profile.md) | A user's public profile and posts |
| [GET `/me`](me.md) | Your full account object |
| [GET `/exists`](exists.md) | Check whether a username exists |
| [GET `/follow`](follow.md) | Follow a user |
| [GET `/unfollow`](unfollow.md) | Unfollow a user |
| [GET `/followers`](followers.md) | A user's followers |
| [GET `/following`](following.md) | Who a user follows |
| [GET `/notifications`](notifications.md) | Your recent notifications |

### Account features

| Endpoint | Description |
| --- | --- |
| [GET `/claim_daily`](claim_daily.md) | Claim your daily credits |
| [GET `/claim_time`](claim_time.md) | Time until your next daily claim |
| [GET `/badges`](badges.md) | Your badges |
| [Friends](friends.md) | Friend requests and friend lists |
| [Files](files.md) | Your OFSF file storage |
| [Items](items.md) | The item marketplace |
| [Gifts](gifts.md) | Credit gifts with redeem codes |
