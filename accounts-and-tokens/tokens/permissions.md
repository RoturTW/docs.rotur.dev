# Permissions

The permissions you can grant to a sub-token, and the predefined groups that bundle them.

## GET `/tokens/permissions`

Returns every permission and every permission group. Also available at `GET /v2/tokens/permissions`.

**Auth:** None.

### Example

```http
GET /tokens/permissions
```

**Response `200`:**

```json
{
  "permissions": [
    "account:delete",
    "account:profile",
    "account:settings",
    "account:view",
    "account:email",
    "account:signins",
    "credits:view",
    "..."
  ],
  "groups": [
    {
      "name": "read_only",
      "description": "Read-only access to your profile, posts, friends, and followers",
      "permissions": ["account:view", "credits:view", "..."]
    }
  ]
}
```

A group is a convenience for building a permission list. When you create or update a token, send the individual permission strings, not the group name.

## All permissions

### Account

| Permission | Description |
|---|---|
| `account:delete` | Delete the user account |
| `account:profile` | Edit profile fields and status, and read the account's friend notes |
| `account:settings` | Change account settings, and read the privacy settings |
| `account:view` | View the account's profile and record. See below. |
| `account:email` | See the account's email address. Adults only. |
| `account:signins` | See the account's sign-in history. Adults only. |

Without `account:view`, a token reading the account (for example with `GET /me`) gets the public profile, the same as anyone signed out sees, plus whatever its other permissions cover. With it, the token also sees the rest of the account record: the fields people set themselves (bio, pronouns, theme and so on) and system fields such as the account ID, badges, banner, status, subscription plan, linked sign-in providers and when the account was last seen.

`account:view` does not cover:

- the email address (`account:email`) or sign-in history (`account:signins`)
- friends and friend requests (`friends:view`), or blocked users (`blocked:view`)
- the credit balance, transactions and purchases (`credits:view`), or items (`items:view`)
- notification settings (`notifications:view`), privacy settings (`account:settings`) or friend notes (`account:profile`)
- date of birth, parental controls, billing, moderation records and credentials, which apps never see

`account:email` and `account:signins` are only honoured for adults. If an account is under 18, or has no date of birth, they are dropped when the token is created or updated, and the data is never returned. Requests still succeed, just without that data. Some profile fields, such as when the account was last seen, are also hidden from apps for under-18s.

### Credits

| Permission | Description |
|---|---|
| `credits:view` | View credit balance and transactions |
| `credits:manage` | Modify credit balance |
| `credits:transfer` | Transfer credits to other users |
| `credits:daily` | Claim daily credits |

### Friends

| Permission | Description |
|---|---|
| `friends:view` | View friends list |
| `friends:manage` | Manage friends list |
| `friends:request` | Send friend requests |
| `friends:accept` | Accept friend requests |
| `friends:remove` | Remove friends |
| `friends:cancel` | Cancel outgoing friend requests |

### Posts

| Permission | Description |
|---|---|
| `posts:view` | View posts |
| `posts:create` | Create new posts |
| `posts:delete` | Delete posts |
| `posts:manage` | Manage posts (edit, pin, etc.) |
| `posts:like` | Like and unlike posts |
| `posts:reply` | Reply to posts |
| `posts:repost` | Repost posts |
| `posts:vote` | Vote in polls |
| `posts:bookmark` | Add and remove saved posts. Reading them needs `posts:view`. |

### Following

| Permission | Description |
|---|---|
| `following:view` | View following list |
| `following:follow` | Follow users |
| `following:unfollow` | Unfollow users |

### Files

| Permission | Description |
|---|---|
| `files:view` | View files |
| `files:manage` | Upload and manage files |
| `files:delete` | Delete files |

### Storage

| Permission | Description |
|---|---|
| `storage:view` | View storage |
| `storage:manage` | Write to storage |
| `storage:delete` | Delete storage |

### Keys

| Permission | Description |
|---|---|
| `keys:view` | View keys |
| `keys:manage` | Create, update, and revoke keys |

### Groups

| Permission | Description |
|---|---|
| `groups:view` | View groups |
| `groups:manage` | Manage groups |
| `groups:join` | Join groups |
| `groups:leave` | Leave groups |
| `groups:members.view` | View group member lists |
| `groups:invite` | Send and manage group invites |

Banning and unbanning group members needs `groups:manage`.

### Notifications

| Permission | Description |
|---|---|
| `notifications:view` | View notifications |
| `notifications:send` | Send notifications |

### Gifts

| Permission | Description |
|---|---|
| `gifts:view` | View gifts |
| `gifts:create` | Create gifts |
| `gifts:claim` | Claim gifts |
| `gifts:cancel` | Cancel gifts |

### Items

| Permission | Description |
|---|---|
| `items:view` | View marketplace items |
| `items:buy` | Buy items |
| `items:sell` | Sell items |
| `items:manage` | Manage items |

### Cosmetics

| Permission | Description |
|---|---|
| `cosmetics:view` | View cosmetics |
| `cosmetics:buy` | Buy cosmetics |
| `cosmetics:equip` | Equip and unequip cosmetics |
| `cosmetics:gift` | Gift cosmetics |

### Emojis

| Permission | Description |
|---|---|
| `emojis:view` | List the account's uploaded and saved custom emojis |
| `emojis:manage` | Upload, save, unsave and delete custom emojis |

### Reports

| Permission | Description |
|---|---|
| `reports:create` | Report content and accounts |

### Other

| Permission | Description |
|---|---|
| `validators:generate` | Generate [validators](../validators.md) |
| `signing:private` | Read the account's private signing key (`GET /v2/me/signing-key`) |
| `blocked:view` | View blocked users list |
| `blocked:manage` | Block and unblock users |
| `tokens:manage` | Manage sub-tokens. Cannot be granted to a sub-token. |

### Permissions that need confirmation

A token holding any of these can move money or read secrets, so giving one to a token asks you to confirm it's you. See [Create a token](create-token.md).

`account:settings`, `credits:manage`, `credits:transfer`, `gifts:create`, `items:buy`, `cosmetics:buy`, `cosmetics:gift`, `signing:private`

***

## Permission groups

Predefined bundles of permissions for common kinds of app.

### `read_only`

**Read-only access to your profile, posts, friends, and followers.**

`account:view`, `credits:view`, `friends:view`, `posts:view`, `following:view`, `files:view`, `storage:view`, `keys:view`, `groups:view`, `groups:members.view`, `notifications:view`, `gifts:view`, `items:view`, `cosmetics:view`, `blocked:view`

### `social`

**Read and interact with posts, friends, and following.**

`account:view`, `credits:view`, `friends:view`, `posts:view`, `posts:create`, `posts:delete`, `posts:manage`, `posts:like`, `posts:reply`, `posts:repost`, `following:view`, `following:follow`, `following:unfollow`, `friends:manage`, `friends:request`, `friends:accept`, `friends:remove`, `friends:cancel`, `notifications:view`, `reports:create`, `posts:vote`, `posts:bookmark`

### `economy`

**Manage credits, gifts, and marketplace items.**

`account:view`, `credits:view`, `credits:manage`, `credits:transfer`, `credits:daily`, `gifts:view`, `gifts:create`, `gifts:claim`, `gifts:cancel`, `items:view`, `items:buy`, `items:sell`, `items:manage`, `cosmetics:view`, `cosmetics:buy`, `cosmetics:equip`, `cosmetics:gift`

### `storage`

**Manage files and storage.**

`account:view`, `files:view`, `files:manage`, `files:delete`, `storage:view`, `storage:manage`, `storage:delete`

### `full`

**Full access to everything except account deletion and token management.**

Every permission above except `account:delete` and `tokens:manage`. That includes `account:email` and `account:signins`, which are still dropped for under-18s.
