# Groups

Groups are Rotur communities with their own roles, announcements, events, role products, invites, and a shared credits balance. Use these endpoints to find, create, join, and run groups.

> **Base URL:** `https://api.rotur.dev/v2/groups`
>
> **Auth:** Send your Rotur token in an `Authorization: Bearer <token>` header. The legacy `auth` query parameter is still accepted. Endpoints marked "Auth: None" work without a token.

`{tag}` in every path is the group's tag. Every endpoint with `{tag}` in its path returns `404` with `{"error": "Group not found"}` when no group has that tag, and every endpoint that needs auth returns `403` when the token is missing or invalid. Those errors aren't repeated on each page.

## Concepts

### Tags

Each group has a unique tag of up to 10 letters and digits (`A-Z`, `a-z`, `0-9`). Paths use the tag, not the group ID. The owner can [change the tag](update.md).

### Token permissions and group permissions

Two separate permission checks apply:

- **Token permissions** (such as `groups:view` or `groups:manage`) only restrict [sub-tokens](../tokens/permissions.md). A main account token passes every token permission check.
- **Group permissions** (such as `groups.roles.manage`) come from the roles you hold in the group. They apply to everyone, whatever token you use.

Each endpoint's **Auth** line lists both.

### Join policies

| Policy | How people join |
| --- | --- |
| `OPEN` | [Join](join.md) directly |
| `REQUEST` | Send a [join request](join-requests.md) that a member with invite permission accepts |
| `INVITE` | Accept an [invite](invites.md) |

### Visibility

Groups are public or private. Only public groups appear in [search](search.md) and [top groups](top.md), and only public groups accept direct joins and join requests. A private group can only be joined by accepting an invite, and only its members can tip it. Anyone can [fetch a group](get.md) by tag.

### Roles and group permissions

A new group has two roles: **Owner** and **Member**. The Owner role grants every group permission, whatever its stored permission list says. The Member role is given to new members. You can [create more roles](roles.md) with any mix of permissions and benefits.

| Permission | Allows |
| --- | --- |
| `groups.manage` | Editing group settings, icon, and banner (same as `groups.group.edit`) |
| `groups.group.edit` | Editing group settings, icon, and banner |
| `groups.members.view` | Listing members |
| `groups.members.invite` | Sending, listing, and revoking invites; listing, accepting, and declining join requests |
| `groups.members.remove` | Kicking members |
| `groups.members.ban` | Banning, unbanning, and listing bans |
| `groups.roles.manage` | Creating, updating, and deleting roles and role products |
| `groups.roles.assign` | Assigning roles to members and removing them |
| `groups.announcements.send` | Posting and deleting announcements |
| `groups.events.manage` | Creating, updating, and deleting events |
| `groups.events.publish` | Publishing events |
| `groups.tips.manage` | Creating and updating fundraising campaigns |
| `groups.tips.withdraw` | Withdrawing from the group balance and viewing withdrawals |
| `groups.tips.deposit` | Nothing yet; no endpoint checks it |

### Readme, rules, and entry fee

A group can have a **readme** (up to 10,000 characters) and **rules** (up to 5,000 characters). Clients typically render the readme as Markdown and show the rules before someone joins. Length limits on this API count bytes, so non-ASCII text uses up the limit faster.

A group can charge an **entry fee** in credits. The fee is taken from the joining user when they join, accept an invite, or have their join request accepted. It's recorded as a `group_entry_fee` transaction and added to the group's `credits_balance`. If they can't afford it, the join fails.

### Credits

Creating a group costs **15 credits**. Tips, entry fees, and role product sales all go into the group's `credits_balance`. Members with `groups.tips.withdraw` can [withdraw](tips.md) from it.

## Endpoints

| Method | Path | Auth | Description |
| --- | --- | --- | --- |
| GET | [`/v2/groups/mine`](mine.md) | Required | List groups you're in |
| GET | [`/v2/groups/search`](search.md) | None | Search public groups |
| GET | [`/v2/groups/top`](top.md) | None | The 10 largest public groups |
| POST | [`/v2/groups`](create.md) | Required | Create a group |
| GET | [`/v2/groups/{tag}`](get.md) | None | Get a group |
| PATCH | [`/v2/groups/{tag}`](update.md) | Required | Update a group |
| DELETE | [`/v2/groups/{tag}`](delete.md) | Required | Delete a group |
| POST | [`/v2/groups/{tag}/join`](join.md) | Required | Join a group |
| POST | [`/v2/groups/{tag}/leave`](leave.md) | Required | Leave a group |
| PUT, DELETE | [`/v2/groups/{tag}/represent`](represent.md) | Required | Show or hide a group on your profile |
| POST | [`/v2/groups/{tag}/report`](report.md) | Required | Report a group |
| POST, GET | [`/v2/groups/{tag}/icon`](icon.md) | Upload only | Upload or fetch the icon |
| POST, GET | [`/v2/groups/{tag}/banner`](banner.md) | Upload only | Upload or fetch the banner |
| GET, POST, DELETE | [`/v2/groups/{tag}/announcements`](announcements.md) | Except listing | Announcements |
| GET, POST, PATCH, DELETE | [`/v2/groups/{tag}/events`](events.md) | Required | Events |
| GET, POST | [`/v2/groups/{tag}/tips`](tips.md) | Required | Tips and withdrawals |
| GET, POST, DELETE | [`/v2/groups/{tag}/products`](products.md) | Except ownership check | Role products and subscriptions |
| GET, POST, PATCH, DELETE | [`/v2/groups/{tag}/roles`](roles.md) | Required | Roles |
| GET, DELETE | [`/v2/groups/{tag}/members`](members.md) | Required | List, look up, and kick members |
| GET, PUT, DELETE | [`/v2/groups/{tag}/members/{userid}/roles`](member-roles.md) | Required | A member's roles, permissions, and benefits |
| GET, PUT, DELETE | [`/v2/groups/{tag}/bans`](bans.md) | Required | Bans |
| GET, POST, DELETE | [`/v2/groups/{tag}/invites`](invites.md) | Required | Invites |
| GET, POST | [`/v2/groups/{tag}/join-requests`](join-requests.md) | Required | Join requests |
| POST | [`/v2/groups/{tag}/transfer/{userid}`](transfer.md) | Required | Transfer ownership |

The API also serves a group activity feed (`GET /v2/groups/{tag}/activity`) and fundraising campaigns (`/v2/groups/{tag}/campaigns`). They aren't documented here yet.

## Data models

### Group

Returned by most group endpoints. `owner_user_id` holds the owner's **username**, not their ID. `created_at` is in Unix seconds.

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "A group for testing",
  "readme": "# About\nWelcome to the group.",
  "rules": "1. Be respectful\n2. No spam",
  "icon_url": "https://api.rotur.dev/groups/mygroup/icon.jpg",
  "banner_url": "https://api.rotur.dev/groups/mygroup/banner",
  "owner_user_id": "alice",
  "public": true,
  "join_policy": "OPEN",
  "entry_fee": 0,
  "created_at": 1717000000,
  "credits_balance": 150.0,
  "member_count": 42
}
```

### Member

```json
{
  "id": "abc-123",
  "group_tag": "mygroup",
  "user_id": "user-id-here",
  "username": "alice",
  "role_ids": ["role-1", "role-2"],
  "joined_at": 1717000000,
  "muted_announcements": false
}
```

### Role

```json
{
  "id": "role-1",
  "group_tag": "mygroup",
  "name": "Moderator",
  "description": "Can post announcements",
  "assign_on_join": false,
  "self_assignable": true,
  "benefits": ["custom_color"],
  "permissions": ["groups.announcements.send"]
}
```

### Announcement

```json
{
  "id": "ann-1",
  "group_tag": "mygroup",
  "title": "Welcome",
  "body": "Glad to have you here.",
  "author_username": "alice",
  "created_at": 1717000000,
  "ping_members": true
}
```

### Event

`start_time` and `end_time` are in Unix seconds. `created_by` is a username.

```json
{
  "id": "evt-1",
  "group_tag": "mygroup",
  "title": "Game Night",
  "description": "Weekly game night",
  "start_time": 1717000000,
  "end_time": 1717014400,
  "location": "Discord",
  "visibility": "MEMBERS",
  "created_by": "alice",
  "published": true
}
```

### Tip

`campaign_id` only appears on tips that were contributions to a fundraising campaign.

```json
{
  "id": "tip-1",
  "group_tag": "mygroup",
  "from_username": "bob",
  "amount_credits": 25.0,
  "note": "keep it up",
  "created_at": 1717000000
}
```
