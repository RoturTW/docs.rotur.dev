# Announcements

Announcements are messages posted to a whole group. Posting one sends a push notification to every member who hasn't muted the group's announcements.

## GET `/v2/groups/{tag}/announcements`

List a group's announcements, newest first. This works for private groups too.

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `limit` | query | integer | No | Maximum number to return. Default `10`; invalid or non-positive values use the default |

### Example

```http
GET /v2/groups/mygroup/announcements?limit=5
```

**Response `200`:** an array of [announcement objects](README.md#announcement), or `null` if there are none.

```json
[
  {
    "id": "ann-1",
    "group_tag": "mygroup",
    "title": "Welcome",
    "body": "Glad to have you here.",
    "author_username": "alice",
    "created_at": 1717000000,
    "ping_members": false
  }
]
```

## POST `/v2/groups/{tag}/announcements`

Post an announcement. Every other member who hasn't muted announcements, and who allows notifications from you, gets a push notification titled `[<tag>] <title>` with source `group_<tag>`.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.announcements.send`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `title` | query | string | Yes | Up to 100 characters |
| `body` | query | string | No | Up to 2,000 characters |
| `ping_members` | query | string | No | `true` to set `ping_members` on the announcement. Default `false`. It doesn't change who is notified |

### Example

```http
POST /v2/groups/mygroup/announcements?title=New%20Event&body=Check%20it%20out
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the new announcement. Unlike the list endpoint, it has `author_user_id` (your user ID) instead of `author_username`.

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

### Errors

| Status | When |
| --- | --- |
| `400` | `title` is missing (`Title is required`) or over 100 characters (`Title length exceeded`) |
| `400` | `body` is over 2,000 characters (`Body length exceeded`) |
| `403` | You lack `groups.announcements.send` (`You don't have permission to send announcements`) |

## DELETE `/v2/groups/{tag}/announcements/{announcementid}`

Delete an announcement.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.announcements.send`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `announcementid` | path | string | Yes | The announcement's `id` |

### Example

```http
DELETE /v2/groups/mygroup/announcements/ann-2
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Announcement deleted"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.announcements.send` (`You don't have permission to delete announcements`) |
| `404` | No announcement has that ID in this group (`Announcement not found`) |

## POST `/v2/groups/{tag}/announcements/mute`

Toggle whether you get push notifications for this group's announcements. Each call flips the setting; read your [member record](members.md) (`muted_announcements`) to see the current value.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be a member.

### Example

```http
POST /v2/groups/mygroup/announcements/mute
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Mute status updated"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | You aren't a member (`You are not a member of this group`) |
