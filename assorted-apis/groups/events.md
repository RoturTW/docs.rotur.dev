# Events

Events are scheduled activities for a group. Each event has a visibility: `MEMBERS` events only show to group members, `PUBLIC` events show to everyone.

## List Events

### GET `/v2/groups/{tag}/events`

**Auth:** required. Token permission: `groups:view`.

Returns the group's events. `MEMBERS` events are filtered out unless you're a member.

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/events?auth=YOUR_TOKEN"
```

**Example response (200):**

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

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |

***

## Create an Event

### POST `/v2/groups/{tag}/events`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.events.manage` group permission. Setting `published=true` also requires `groups.events.publish`.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `title` | string | Yes | Event title (max 100 chars) |
| `description` | string | No | Event description (max 500 chars) |
| `start_time` | int | Yes | Unix timestamp for the start, must be in the future |
| `duration_hours` | int | No | Duration in hours, 1 to 72 (default: 1) |
| `location` | string | No | Event location (max 200 chars) |
| `visibility` | string | No | `"MEMBERS"` or `"PUBLIC"` (default: `"MEMBERS"`) |
| `published` | string | No | `"true"` to publish immediately (default: `"false"`) |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/events?auth=YOUR_TOKEN&title=Tournament&start_time=1717100000&duration_hours=4"
```

**Example response (201):**

```json
{
  "id": "evt-2",
  "group_tag": "mygroup",
  "title": "Tournament",
  "description": "",
  "start_time": 1717100000,
  "end_time": 1717114400,
  "location": "",
  "visibility": "MEMBERS",
  "created_by": "user-id-here",
  "published": false
}
```

{% hint style="info" %}
The create response contains the creator's raw user ID in `created_by`. The list and update endpoints return the username instead.
{% endhint %}

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Title is required` | No title provided |
| 400 | `Invalid start time` | Missing, invalid, or in the past |
| 400 | `Invalid duration (must be 1-72 hours)` | Duration out of range |
| 400 | `Invalid visibility` | Not `MEMBERS` or `PUBLIC` |
| 403 | `You don't have permission to manage events` | Missing `groups.events.manage` |
| 403 | `You don't have permission to publish events` | `published=true` without `groups.events.publish` |
| 404 | `Group not found` | Group doesn't exist |

***

## Update an Event

### PATCH `/v2/groups/{tag}/events/{eventid}`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.events.manage` group permission.

**Body (JSON):**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | string | No | New title (max 100 chars) |
| `description` | string | No | New description (max 500 chars) |
| `location` | string | No | New location (max 200 chars) |
| `start_time` | int | No | New start (future Unix timestamp). Keeps the current duration |
| `duration_hours` | int | No | New duration, 1 to 72 hours |
| `visibility` | string | No | `"MEMBERS"` or `"PUBLIC"` |
| `published` | bool | No | Publish or unpublish (publishing requires `groups.events.publish`) |

**Example request:**

```bash
curl -X PATCH "https://api.rotur.dev/v2/groups/mygroup/events/evt-2?auth=YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title": "Big Tournament", "published": true}'
```

**Example response (200):** the updated event, with `created_by` resolved to a username.

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Invalid request body` | Malformed JSON |
| 400 | `Invalid start time` | Start time in the past |
| 403 | `You don't have permission to manage events` | Missing `groups.events.manage` |
| 403 | `You don't have permission to publish events` | Publishing without `groups.events.publish` |
| 404 | `Event not found` | Event ID doesn't exist in this group |
| 404 | `Group not found` | Group doesn't exist |

***

## Delete an Event

### DELETE `/v2/groups/{tag}/events/{eventid}`

**Auth:** required. Token permission: `groups:manage`. Requires the `groups.events.manage` group permission.

**Example request:**

```bash
curl -X DELETE "https://api.rotur.dev/v2/groups/mygroup/events/evt-2?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
{
  "message": "Event deleted"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to manage events` | Missing `groups.events.manage` |
| 404 | `Event not found` | Event ID doesn't exist in this group |
| 404 | `Group not found` | Group doesn't exist |
