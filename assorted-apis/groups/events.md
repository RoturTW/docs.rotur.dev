# Events

## List Events

### GET `/groups/{tag}/events`

Returns events for a group. Members-only events (`visibility: "MEMBERS"`) are only visible to group members.

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
[
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
]
```

**Event Visibility:**

| Value | Description |
|-------|-------------|
| `MEMBERS` | Only visible to group members |
| `PUBLIC` | Visible to everyone |

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Group tag is required` | No tag provided |
| 404 | `Group not found` | Group doesn't exist |

---

## Create an Event

### POST `/groups/{tag}/events`

Create a new group event. Requires `groups.events.manage` permission. Publishing additionally requires `groups.events.publish`.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `title` | string | Yes | Event title (max 100 chars) |
| `description` | string | No | Event description (max 500 chars) |
| `start_time` | int64 | Yes | Unix timestamp for start time (must be in the future) |
| `duration_hours` | int | No | Duration in hours, 1–72 (default: 1) |
| `location` | string | No | Event location (max 200 chars) |
| `visibility` | string | No | `"MEMBERS"` or `"PUBLIC"` (default: `"MEMBERS"`) |
| `published` | string | No | `"true"` to publish immediately (default: `"false"`) |

**Response (201):**

```json
{
  "id": "evt-2",
  "group_tag": "mygroup",
  "title": "Tournament",
  "description": "Weekly tournament",
  "start_time": 1717100000,
  "end_time": 1717114400,
  "location": "In-game",
  "visibility": "PUBLIC",
  "created_by": "alice",
  "published": true
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Title is required` | No title provided |
| 400 | `Title length exceeded` | Title longer than 100 chars |
| 400 | `Description length exceeded` | Description longer than 500 chars |
| 400 | `Location length exceeded` | Location longer than 200 chars |
| 400 | `Invalid start time` | Not a valid future timestamp |
| 400 | `Invalid duration (must be 1-72 hours)` | Duration out of range |
| 400 | `Invalid visibility` | Not `MEMBERS` or `PUBLIC` |
| 403 | `You don't have permission to manage events` | Missing `groups.events.manage` |
| 403 | `You don't have permission to publish events` | Missing `groups.events.publish` and `published=true` |
| 404 | `Group not found` | Group doesn't exist |
