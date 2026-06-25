# Groups

The Rotur groups system lets users create and manage communities. Groups have their own roles, permissions, announcements, events, and a shared credits balance from tips.

> **Base URL:** `https://api.rotur.dev/groups`
>
> **Authentication:** Endpoints that modify your account require a valid `auth` query parameter (your Rotur user token).

***

## Core Concepts

### Group Tags

Every group has a unique **tag** — a short, alphanumeric identifier (max 20 characters). Tags are used in API paths instead of group IDs for readability.

### Join Policies

| Policy | Description |
|--------|-------------|
| `OPEN` | Anyone can join |
| `REQUEST` | Users must request to join (not yet implemented) |
| `INVITE` | Invite-only (not yet implemented) |

### Visibility

Groups can be **public** or **private**. Only public groups appear in search results and the top-groups leaderboard. Private groups require membership to view.

### Roles & Permissions

Each group has an **Owner** role (created automatically) and a default **Member** role. The Owner can create additional custom roles with specific permissions and benefits.

> **Note:** The Owner role always has **all** group permissions dynamically — it doesn't rely on a stored permission list. This means any new permissions added in future updates are automatically available to the owner without needing to update the group's role data.

**Available permissions:**

| Permission | Description |
|------------|-------------|
| `groups.manage` | Full group management |
| `groups.members.invite` | Invite new members |
| `groups.members.remove` | Remove members |
| `groups.members.ban` | Ban/unban members |
| `groups.members.view` | View the group member list |
| `groups.roles.manage` | Create, update, delete roles |
| `groups.roles.assign` | Assign/remove roles from members |
| `groups.announcements.send` | Create and delete announcements |
| `groups.events.manage` | Create events |
| `groups.events.publish` | Publish events |
| `groups.tips.manage` | Manage tips |
| `groups.tips.withdraw` | Withdraw credits from the group tip jar |
| `groups.tips.deposit` | Deposit credits into the group tip jar |
| `groups.group.edit` | Edit group settings |

### Readme & Rules

Groups can have a **readme** (long-form description, max 10,000 characters) and **rules** (max 5,000 characters). The readme is typically rendered as markdown and displayed on the group's page. Rules are shown to users before they join — the client is responsible for presenting the rules and requiring agreement before allowing the join request.

### Entry Fee

Groups can charge an **entry fee** in credits. When a user joins a group with an entry fee:
- The fee is deducted from the user's credits
- The fee is added to the group's `credits_balance`
- A `group_entry_fee` transaction is recorded on the user's account

If the user doesn't have enough credits, the join request is rejected.

### Credits

- **Creating a group** costs **50 credits**.
- Banners are uploaded directly (no credit charge).
- Tips sent to a group are added to the group's `credits_balance`. Tips are stored in a separate `tips.json` file per group. Users with the `groups.tips.withdraw` permission can withdraw credits from the tip jar to their own balance. Withdrawals are stored in a separate `withdrawals.json` file per group.

### Icons

Group icons can be uploaded as images and are automatically resized to **256×256 JPEG** (max 5MB upload). Icons are served at `GET /groups/{tag}/icon.jpg`.

### Banners

Group banners can be uploaded as images and are automatically resized to **900×300** (max 5MB upload). GIF, PNG, and JPEG formats are supported. Banners are served at `GET /groups/{tag}/banner`.

### Announcements & Notifications

When a member with the `groups.announcements.send` permission creates an announcement, the system sends **push notifications** to all group members who haven't muted announcements. The notification source is `group_{tag}`, so members can control notification preferences per group.

### Group Representation

Users can **represent** a group, which sets `sys.group` on their account. This also appears as `group_tag` in their profile response from `GET /profile`.

### Storage

Each group is stored in its own directory:

```
groups/{groupId}/
├── group.json       # Group metadata, members, roles, announcements, events
└── tips.json        # Tips (separate file)
```

If an icon and/or banner is uploaded:

```
groups/{groupId}/
├── group.json
├── tips.json
├── withdrawals.json
├── icon.jpg
└── banner.jpg (or .png / .gif)
```

***

## Endpoints

| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| [`/groups/mine`](mine.md) | GET | Yes | List groups you're a member of |
| [`/groups/search`](search.md) | GET | Yes | Search public groups |
| [`/groups/top`](top.md) | GET | No | Top 10 groups by member count |
| [`/groups/create`](create.md) | POST | Yes | Create a new group (50 credits) |
| [`/groups/{tag}`](get.md) | GET | Yes | Get a group's public info |
| [`/groups/{tag}`](update.md) | PATCH | Yes | Update group settings (owner only) |
| [`/groups/{tag}`](delete.md) | DELETE | Yes | Delete a group (owner only) |
| [`/groups/{tag}/join`](join.md) | POST | Yes | Join a public group |
| [`/groups/{tag}/leave`](leave.md) | POST | Yes | Leave a group |
| [`/groups/{tag}/rep`](represent.md) | POST | Yes | Represent a group on your profile |
| [`/groups/{tag}/disrep`](represent.md) | POST | Yes | Stop representing a group |
| [`/groups/{tag}/report`](report.md) | POST | Yes | Report a group |
| [`/groups/{tag}/icon`](icon.md) | POST | Yes | Upload a group icon (owner only) |
| [`/groups/{tag}/icon.jpg`](icon.md) | GET | No | Get a group's icon image |
| [`/groups/{tag}/banner`](banner.md) | POST | Yes | Upload a group banner image (owner only) |
| [`/groups/{tag}/banner`](banner.md) | GET | No | Get a group's banner image |
| [`/groups/{tag}/announcements`](announcements.md) | GET | No | List announcements |
| [`/groups/{tag}/announcements`](announcements.md) | POST | Yes | Create an announcement |
| [`/groups/{tag}/announcements/{id}`](announcements.md) | DELETE | Yes | Delete an announcement |
| [`/groups/{tag}/announcements/mute`](announcements.md) | POST | Yes | Toggle announcement mute |
| [`/groups/{tag}/events`](events.md) | GET | Yes | List events |
| [`/groups/{tag}/events`](events.md) | POST | Yes | Create an event |
| [`/groups/{tag}/tips`](tips.md) | GET | Yes | List tips |
| [`/groups/{tag}/tips`](tips.md) | POST | Yes | Send a tip |
| [`/groups/{tag}/tips/withdraw`](tips.md) | POST | Yes | Withdraw from the group tip jar |
| [`/groups/{tag}/tips/withdrawals`](tips.md) | GET | Yes | List tip jar withdrawals |
| [`/groups/{tag}/roles`](roles.md) | GET | Yes | List roles |
| [`/groups/{tag}/roles`](roles.md) | POST | Yes | Create a role |
| [`/groups/{tag}/roles/{id}`](roles.md) | PATCH | Yes | Update a role |
| [`/groups/{tag}/roles/{id}`](roles.md) | DELETE | Yes | Delete a role |
| [`/groups/{tag}/members`](members.md) | GET | Yes | List all members (paginated, owner or `groups.members.view` permission) |
| [`/groups/{tag}/members/{user}`](members.md) | GET | Yes | Get a member's detailed info |
| [`/groups/{tag}/members/{user}`](members.md) | DELETE | Yes | Kick a member (`groups.members.remove`) |
| [`/groups/{tag}/members/{user}/ban`](members.md) | POST | Yes | Ban a member (`groups.members.ban`) |
| [`/groups/{tag}/members/{user}/ban`](members.md) | DELETE | Yes | Unban a member (`groups.members.ban`) |
| [`/groups/{tag}/bans`](members.md) | GET | Yes | List bans (`groups.members.ban`) |
| [`/groups/{tag}/bans/{user}`](members.md) | GET | Yes | Check if a user is banned |
| [`/groups/{tag}/invites`](members.md) | GET | Yes | List pending invites |
| [`/groups/{tag}/invites`](members.md) | POST | Yes | Send an invite (`groups.members.invite`) |
| [`/groups/{tag}/invites/{id}/accept`](members.md) | POST | Yes | Accept an invite |
| [`/groups/{tag}/invites/{id}/decline`](members.md) | POST | Yes | Decline an invite |
| [`/groups/{tag}/invites/{id}`](members.md) | DELETE | Yes | Revoke an invite (`groups.members.invite`) |
| [`/groups/invites/mine`](members.md) | GET | Yes | List your pending invites |
| [`/groups/{tag}/join_requests`](members.md) | GET | Yes | List join requests (`groups.members.invite`) |
| [`/groups/{tag}/join_requests`](members.md) | POST | Yes | Request to join |
| [`/groups/{tag}/join_requests/{id}/accept`](members.md) | POST | Yes | Accept a join request (`groups.members.invite`) |
| [`/groups/{tag}/join_requests/{id}/decline`](members.md) | POST | Yes | Decline a join request (`groups.members.invite`) |
| [`/groups/{tag}/transfer/{user}`](members.md) | POST | Yes | Transfer ownership (owner only) |
| [`/groups/{tag}/members/{user}/roles`](member-roles.md) | GET | Yes | Get a member's roles |
| [`/groups/{tag}/members/{user}/permissions`](member-roles.md) | GET | Yes | Get a member's permissions |
| [`/groups/{tag}/members/{user}/benefits`](member-roles.md) | GET | Yes | Get a member's benefits |
| [`/groups/{tag}/members/{user}/roles/{id}`](member-roles.md) | POST | Yes | Assign a role to a member |
| [`/groups/{tag}/members/{user}/roles/{id}`](member-roles.md) | DELETE | Yes | Remove a role from a member |

***

## Data Models

### Group (public response)

All group API responses return the **public** version with `owner_user_id` resolved to a username:

```json
{
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

### Member (API response)

Member responses use `username` instead of `user_id`:

```json
{
  "id": "abc-123",
  "group_tag": "mygroup",
  "username": "alice",
  "role_ids": ["role-1", "role-2"],
  "joined_at": 1717000000,
  "muted_announcements": false
}
```

### Announcement (API response)

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

### Event (API response)

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

### Tip (API response)

```json
{
  "id": "tip-1",
  "group_tag": "mygroup",
  "from_username": "bob",
  "amount_credits": 25.0,
  "created_at": 1717000000
}
```

### Withdrawal (API response)

```json
{
  "id": "withdrawal-1",
  "group_tag": "mygroup",
  "to_username": "alice",
  "amount_credits": 50.0,
  "created_at": 1717200000
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
