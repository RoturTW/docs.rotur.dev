# Groups

Groups let Rotur users create and manage communities. Each group has its own roles, permissions, announcements, events, products, invites, and a shared credits balance.

> **Base URL:** `https://api.rotur.dev/v2/groups`
>
> **Authentication:** Send your Rotur token as an `Authorization: Bearer` header (preferred). `auth` query auth is still accepted as a legacy fallback. Endpoints below that say "Auth: required" reject requests without a valid token.

{% hint style="info" %}
Two kinds of permissions apply to groups:

* **Token permissions** (like `groups:view` or `groups:manage`) only matter for scoped sub-tokens. A normal account token passes every token permission check automatically.
* **Group permissions** (like `groups.roles.manage`) come from the roles you hold inside a group. These are checked for everyone.
{% endhint %}

***

## Core Concepts

### Group Tags

Every group has a unique **tag**: a short alphanumeric identifier, up to 10 characters. Tags are used in API paths instead of group IDs.

### Join Policies

| Policy | Description |
|--------|-------------|
| `OPEN` | Anyone can join a public group directly |
| `REQUEST` | Users must send a [join request](join-requests.md) and be accepted |
| `INVITE` | Users can only join with a pending [invite](invites.md) |

### Visibility

Groups are **public** or **private**. Only public groups appear in search results and the top-groups list, and only public groups can be joined or tipped by non-members. Anyone can still fetch a group's info by tag.

### Roles and Group Permissions

Each group starts with an **Owner** role and a **Member** role. The Owner role always grants every group permission. You can create custom roles with any mix of permissions and benefits.

| Permission | Description |
|------------|-------------|
| `groups.manage` | Full group management |
| `groups.members.invite` | Invite members, handle invites and join requests |
| `groups.members.remove` | Kick members |
| `groups.members.ban` | Ban and unban members, view bans |
| `groups.members.view` | View the member list |
| `groups.roles.manage` | Create, update, and delete roles and products |
| `groups.roles.assign` | Assign and remove roles from members |
| `groups.announcements.send` | Create and delete announcements |
| `groups.events.manage` | Create, update, and delete events |
| `groups.events.publish` | Publish events |
| `groups.tips.manage` | Manage tips |
| `groups.tips.withdraw` | Withdraw from the group tip jar, view withdrawals |
| `groups.tips.deposit` | Deposit into the group tip jar |
| `groups.group.edit` | Edit group settings, icon, and banner |

### Readme and Rules

Groups can have a **readme** (long-form description, max 10,000 characters) and **rules** (max 5,000 characters). The readme is typically rendered as markdown on the group page. Clients should show the rules before a user joins.

### Entry Fee

Groups can charge an **entry fee** in credits. When you join a group with an entry fee, the fee is deducted from your balance, added to the group's `credits_balance`, and recorded as a `group_entry_fee` transaction. If you can't afford it, the join fails.

### Credits

* Creating a group costs **50 credits**.
* Tips, entry fees, and product purchases all flow into the group's `credits_balance`.
* Members with `groups.tips.withdraw` can withdraw from the balance.

***

## Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| [`/v2/groups/mine`](mine.md) | GET | Yes | List groups you're a member of |
| [`/v2/groups/search`](search.md) | GET | Yes | Search public groups |
| [`/v2/groups/top`](top.md) | GET | No | Top 10 groups by member count |
| [`/v2/groups`](create.md) | POST | Yes | Create a group (50 credits) |
| [`/v2/groups/{tag}`](get.md) | GET | No | Get a group's info |
| [`/v2/groups/{tag}`](update.md) | PATCH | Yes | Update group settings |
| [`/v2/groups/{tag}`](delete.md) | DELETE | Yes | Delete a group (owner only) |
| [`/v2/groups/{tag}/join`](join.md) | POST | Yes | Join a public group |
| [`/v2/groups/{tag}/leave`](leave.md) | POST | Yes | Leave a group |
| [`/v2/groups/{tag}/represent`](represent.md) | PUT / DELETE | Yes | Represent or stop representing a group |
| [`/v2/groups/{tag}/report`](report.md) | POST | Yes | Report a group |
| [`/v2/groups/{tag}/icon`](icon.md) | POST / GET | Mixed | Upload or fetch the group icon |
| [`/v2/groups/{tag}/banner`](banner.md) | POST / GET | Mixed | Upload or fetch the group banner |
| [`/v2/groups/{tag}/announcements`](announcements.md) | GET / POST / DELETE | Mixed | Announcements |
| [`/v2/groups/{tag}/events`](events.md) | GET / POST / PATCH / DELETE | Yes | Events |
| [`/v2/groups/{tag}/tips`](tips.md) | GET / POST | Yes | Tips and withdrawals |
| [`/v2/groups/{tag}/products`](products.md) | GET / POST / DELETE | Mixed | Purchasable role products and subscriptions |
| [`/v2/groups/{tag}/roles`](roles.md) | GET / POST / PATCH / DELETE | Yes | Roles |
| [`/v2/groups/{tag}/members`](members.md) | GET / DELETE | Yes | Member list, member info, kicking |
| [`/v2/groups/{tag}/members/{user}/roles`](member-roles.md) | GET / PUT / DELETE | Yes | Member roles, permissions, and benefits |
| [`/v2/groups/{tag}/bans`](bans.md) | GET / PUT / DELETE | Yes | Bans |
| [`/v2/groups/{tag}/invites`](invites.md) | GET / POST / DELETE | Yes | Invites |
| [`/v2/groups/{tag}/join-requests`](join-requests.md) | GET / POST | Yes | Join requests |
| [`/v2/groups/{tag}/transfer/{userid}`](transfer.md) | POST | Yes | Transfer ownership |

***

## Data Models

### Group

Group responses resolve `owner_user_id` to a username and include `member_count`:

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "A cool group",
  "readme": "# Welcome\nThis is the group readme...",
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
  "description": "Can manage announcements",
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
  "title": "Welcome!",
  "body": "Glad to have you here.",
  "author_username": "alice",
  "created_at": 1717000000,
  "ping_members": true
}
```

### Event

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

```json
{
  "id": "tip-1",
  "group_tag": "mygroup",
  "from_username": "bob",
  "amount_credits": 25.0,
  "note": "keep it up!",
  "created_at": 1717000000
}
```
