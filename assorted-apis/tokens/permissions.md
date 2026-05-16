# Permissions

> **Authentication:** None (public endpoint)

### GET `/tokens/permissions`

**Description:**
List all available token permissions and permission groups. This endpoint is unauthenticated — anyone can view the permission schema.

**Example:**

```http
GET /tokens/permissions
```

**Response (200):**

```json
{
  "permissions": [
    "account:delete",
    "account:profile",
    "account:settings",
    "account:view",
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

***

## All Permissions

### Account

| Permission | String | Description |
|---|---|---|
| `PermDeleteAccount` | `account:delete` | Delete the user account. **Cannot be granted to sub-tokens.** |
| `PermManageProfile` | `account:profile` | Edit profile fields |
| `PermManageSettings` | `account:settings` | Change account settings |
| `PermViewProfile` | `account:view` | View profile data |

### Credits

| Permission | String | Description |
|---|---|---|
| `PermViewCredits` | `credits:view` | View credit balance and transactions |
| `PermManageCredits` | `credits:manage` | Modify credit balance |
| `PermTransferCredits` | `credits:transfer` | Transfer credits to other users |
| `PermClaimDaily` | `credits:daily` | Claim daily credits |

### Friends

| Permission | String | Description |
|---|---|---|
| `PermViewFriends` | `friends:view` | View friends list |
| `PermManageFriends` | `friends:manage` | Manage friends list |
| `PermSendFriendReq` | `friends:request` | Send friend requests |
| `PermAcceptFriend` | `friends:accept` | Accept friend requests |
| `PermRemoveFriend` | `friends:remove` | Remove friends |

### Posts

| Permission | String | Description |
|---|---|---|
| `PermViewPosts` | `posts:view` | View posts |
| `PermCreatePost` | `posts:create` | Create new posts |
| `PermDeletePost` | `posts:delete` | Delete posts |
| `PermManagePosts` | `posts:manage` | Manage posts (pin, etc.) |
| `PermLikePost` | `posts:like` | Like/unlike posts |
| `PermReplyPost` | `posts:reply` | Reply to posts |
| `PermRepost` | `posts:repost` | Repost posts |

### Following

| Permission | String | Description |
|---|---|---|
| `PermViewFollowing` | `following:view` | View following list |
| `PermFollow` | `following:follow` | Follow users |
| `PermUnfollow` | `following:unfollow` | Unfollow users |

### Files

| Permission | String | Description |
|---|---|---|
| `PermViewFiles` | `files:view` | View files |
| `PermManageFiles` | `files:manage` | Upload and manage files |
| `PermDeleteFiles` | `files:delete` | Delete files |

### Keys

| Permission | String | Description |
|---|---|---|
| `PermViewKeys` | `keys:view` | View keys |
| `PermManageKeys` | `keys:manage` | Create, update, and revoke keys |

### Groups

| Permission | String | Description |
|---|---|---|
| `PermViewGroups` | `groups:view` | View groups |
| `PermManageGroups` | `groups:manage` | Manage groups |
| `PermJoinGroup` | `groups:join` | Join groups |
| `PermLeaveGroup` | `groups:leave` | Leave groups |

### Notifications

| Permission | String | Description |
|---|---|---|
| `PermViewNotifications` | `notifications:view` | View notifications |
| `PermSendNotifications` | `notifications:send` | Send notifications |

### Gifts

| Permission | String | Description |
|---|---|---|
| `PermViewGifts` | `gifts:view` | View gifts |
| `PermCreateGift` | `gifts:create` | Create gifts |
| `PermClaimGift` | `gifts:claim` | Claim gifts |
| `PermCancelGift` | `gifts:cancel` | Cancel gifts |

### Items

| Permission | String | Description |
|---|---|---|
| `PermViewItems` | `items:view` | View marketplace items |
| `PermBuyItems` | `items:buy` | Buy items |
| `PermSellItems` | `items:sell` | Sell items |
| `PermManageItems` | `items:manage` | Manage items |

### Other

| Permission | String | Description |
|---|---|---|
| `PermGenerateValidator` | `validators:generate` | Generate validators |
| `PermViewBlocked` | `blocked:view` | View blocked users list |
| `PermManageBlocked` | `blocked:manage` | Block and unblock users |
| `PermManageTokens` | `tokens:manage` | Manage sub-tokens. **Cannot be granted to sub-tokens.** |

***

## Permission Groups

Permission groups are pre-defined bundles of permissions for common use cases.

### `read_only`

**Read-only access to your profile, posts, friends, and followers.**

| Permission |
|---|
| `account:view` |
| `credits:view` |
| `friends:view` |
| `posts:view` |
| `following:view` |
| `files:view` |
| `keys:view` |
| `groups:view` |
| `notifications:view` |
| `gifts:view` |
| `items:view` |
| `blocked:view` |

### `social`

**Read and interact with posts, friends, and following.**

| Permission |
|---|
| `account:view` |
| `credits:view` |
| `friends:view` |
| `posts:view` |
| `posts:create` |
| `posts:delete` |
| `posts:manage` |
| `posts:like` |
| `posts:reply` |
| `posts:repost` |
| `following:view` |
| `following:follow` |
| `following:unfollow` |
| `friends:manage` |
| `friends:request` |
| `friends:accept` |
| `friends:remove` |
| `notifications:view` |

### `economy`

**Manage credits, gifts, and marketplace items.**

| Permission |
|---|
| `account:view` |
| `credits:view` |
| `credits:manage` |
| `credits:transfer` |
| `credits:daily` |
| `gifts:view` |
| `gifts:create` |
| `gifts:claim` |
| `gifts:cancel` |
| `items:view` |
| `items:buy` |
| `items:sell` |
| `items:manage` |

### `storage`

**Manage files and storage.**

| Permission |
|---|
| `account:view` |
| `files:view` |
| `files:manage` |
| `files:delete` |

### `full`

**Full access to everything except account deletion and token management.**

Includes all permissions **except**:
- `account:delete`
- `tokens:manage`
