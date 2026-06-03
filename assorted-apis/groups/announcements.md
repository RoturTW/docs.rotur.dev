# Announcements

Announcements are messages posted by members with the `groups.announcements.send` permission. When a new announcement is created, push notifications are sent to all non-muted members via the Rotur notification system.

## List Announcements

### GET `/groups/{tag}/announcements`

Returns announcements for a group, newest first.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `limit` | int | No | Max number of results (default: 10) |

**Response (200):**

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

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Group tag is required` | No tag provided |
| 404 | `Group not found` | Group doesn't exist |

---

## Create an Announcement

### POST `/groups/{tag}/announcements`

Create a new announcement. Requires `groups.announcements.send` permission. Push notifications are sent to all non-muted group members.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `title` | string | Yes | Announcement title (max 100 chars) |
| `body` | string | No | Announcement body (max 2000 chars) |
| `ping_members` | string | No | `"true"` to ping members (default: `"false"`) |

**Response (201):**

```json
{
  "id": "ann-2",
  "group_tag": "mygroup",
  "title": "New Event!",
  "body": "Check out our new event.",
  "author_username": "alice",
  "created_at": 1717100000,
  "ping_members": true
}
```

**Push Notifications:** The system sends a notification with source `group_{tag}` to each member who:
- Is not the announcement author
- Has not muted announcements for this group
- Has allowed notifications from the author via `isNotifyAllowed`

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Title is required` | No title provided |
| 400 | `Title length exceeded` | Title longer than 100 chars |
| 400 | `Body length exceeded` | Body longer than 2000 chars |
| 403 | `You don't have permission to send announcements` | Missing `groups.announcements.send` permission |
| 404 | `Group not found` | Group doesn't exist |

---

## Delete an Announcement

### DELETE `/groups/{tag}/announcements/{announcementid}`

Delete an announcement. Requires `groups.announcements.send` permission.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |
| `announcementid` | string | Yes | The announcement ID |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "message": "Announcement deleted"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to delete announcements` | Missing permission |
| 404 | `Announcement not found` | Announcement ID doesn't exist |
| 404 | `Group not found` | Group doesn't exist |

---

## Toggle Announcement Mute

### POST `/groups/{tag}/announcements/mute`

Toggle whether you receive push notifications for announcements in this group.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

```json
{
  "message": "Mute status updated"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are not a member of this group` | Not a member |
| 404 | `Group not found` | Group doesn't exist |
