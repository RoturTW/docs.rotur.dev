# Announcements

Announcements are messages posted to the whole group. New announcements trigger push notifications to every member who hasn't muted them.

## List Announcements

### GET `/v2/groups/{tag}/announcements`

Returns announcements, newest first. No authentication needed.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `limit` | int | No | Max number of results (default: 10) |

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/announcements?limit=5"
```

**Example response (200):**

```json
[
  {
    "id": "ann-1",
    "group_tag": "mygroup",
    "title": "Welcome!",
    "body": "Glad to have you here.",
    "author_username": "alice",
    "created_at": 1717000000,
    "ping_members": false
  }
]
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |

***

## Create an Announcement

### POST `/v2/groups/{tag}/announcements`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.announcements.send` group permission.

Push notifications (source `group_{tag}`) go to every member who hasn't muted announcements and allows notifications from you.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | string | Yes | Announcement title (max 100 chars) |
| `body` | string | No | Announcement body (max 2,000 chars) |
| `ping_members` | string | No | `"true"` to ping members (default: `"false"`) |

**Example request:**

```bash
curl -X POST -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/announcements?title=New%20Event&body=Check%20it%20out"
```

**Example response (201):**

```json
{
  "id": "ann-2",
  "group_tag": "mygroup",
  "title": "New Event",
  "body": "Check it out",
  "author_user_id": "user-id-here",
  "created_at": 1717100000,
  "ping_members": false
}
```

{% hint style="info" %}
The create response contains `author_user_id` (the raw user ID). The list endpoint returns `author_username` instead.
{% endhint %}

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Title is required` | No title provided |
| 400 | `Title length exceeded` | Title longer than 100 chars |
| 400 | `Body length exceeded` | Body longer than 2,000 chars |
| 403 | `You don't have permission to send announcements` | Missing `groups.announcements.send` |
| 404 | `Group not found` | Group doesn't exist |

***

## Delete an Announcement

### DELETE `/v2/groups/{tag}/announcements/{announcementid}`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.announcements.send` group permission.

**Example request:**

```bash
curl -X DELETE -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/announcements/ann-2"
```

**Example response (200):**

```json
{
  "message": "Announcement deleted"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to delete announcements` | Missing permission |
| 404 | `Announcement not found` | Announcement ID doesn't exist |
| 404 | `Group not found` | Group doesn't exist |

***

## Toggle Announcement Mute

### POST `/v2/groups/{tag}/announcements/mute`

**Auth:** required. Token permission: `groups:manage`. You must be a member.

Toggles whether you receive announcement push notifications for this group.

**Example request:**

```bash
curl -X POST -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/announcements/mute"
```

**Example response (200):**

```json
{
  "message": "Mute status updated"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are not a member of this group` | Not a member |
| 404 | `Group not found` | Group doesn't exist |
