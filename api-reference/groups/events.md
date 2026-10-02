# Events

Events are scheduled activities in a group. Each event has a visibility: `MEMBERS` events are only shown to group members, and `PUBLIC` events are shown to anyone who lists the group's events.

## GET `/v2/groups/{tag}/events`

List a group's events, in no particular order. If you aren't a member, `MEMBERS` events are left out. Unpublished events are included.

**Auth:** Required. Sub-tokens need `groups:view`.

### Example

```http
GET /v2/groups/mygroup/events
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of [event objects](README.md#event), or `null` if there are none you can see.

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

## POST `/v2/groups/{tag}/events`

Create an event.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.events.manage`, and also `groups.events.publish` when `published` is `true`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `title` | query | string | Yes | Up to 100 characters |
| `description` | query | string | No | Up to 500 characters |
| `start_time` | query | integer | Yes | Start as a Unix timestamp in seconds. Must be in the future |
| `duration_hours` | query | integer | No | 1–72. Default `1`. Sets `end_time` |
| `location` | query | string | No | Up to 200 characters |
| `visibility` | query | string | No | `MEMBERS` or `PUBLIC`. Default `MEMBERS` |
| `published` | query | string | No | `true` to publish immediately. Default `false` |

### Example

```http
POST /v2/groups/mygroup/events?title=Tournament&start_time=1717100000&duration_hours=4
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the new event. Unlike the other event endpoints, `created_by` is your user ID rather than your username.

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

### Errors

| Status | When |
| --- | --- |
| `400` | `title` is missing (`Title is required`) or over 100 characters (`Title length exceeded`) |
| `400` | `description` or `location` is too long (`Description length exceeded`, `Location length exceeded`) |
| `400` | `start_time` is missing, not a number, or not in the future (`Invalid start time`) |
| `400` | `duration_hours` is outside 1–72 (`Invalid duration (must be 1-72 hours)`) |
| `400` | `visibility` isn't `MEMBERS` or `PUBLIC` (`Invalid visibility`) |
| `403` | You lack `groups.events.manage` (`You don't have permission to manage events`) |
| `403` | `published` is `true` and you lack `groups.events.publish` (`You don't have permission to publish events`) |

## PATCH `/v2/groups/{tag}/events/{eventid}`

Update an event. Only the fields you send are changed.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.events.manage`, and also `groups.events.publish` to set `published` to `true`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `eventid` | path | string | Yes | The event's `id` |
| `title` | body | string | No | 1–100 characters |
| `description` | body | string | No | Up to 500 characters |
| `location` | body | string | No | Up to 200 characters |
| `start_time` | body | number | No | New start as a future Unix timestamp in seconds. The event keeps its current duration |
| `duration_hours` | body | number | No | 1–72. Applied after `start_time` |
| `visibility` | body | string | No | `MEMBERS` or `PUBLIC` |
| `published` | body | boolean | No | Publish or unpublish |

### Example

```http
PATCH /v2/groups/mygroup/events/evt-2
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "title": "Big Tournament", "published": true }
```

**Response `200`:** the updated [event object](README.md#event), with `created_by` as a username.

### Errors

| Status | When |
| --- | --- |
| `400` | The body isn't valid JSON (`Invalid request body`) |
| `400` | `title` is empty (`Title is required`) or a field is too long (`Title length exceeded`, `Description length exceeded`, `Location length exceeded`) |
| `400` | `start_time` isn't in the future (`Invalid start time`) |
| `400` | `duration_hours` is outside 1–72 (`Invalid duration (must be 1-72 hours)`) |
| `400` | `visibility` isn't `MEMBERS` or `PUBLIC` (`Invalid visibility`) |
| `403` | You lack `groups.events.manage` (`You don't have permission to manage events`) |
| `403` | `published` is `true` and you lack `groups.events.publish` (`You don't have permission to publish events`) |
| `404` | No event has that ID in this group (`Event not found`) |

## DELETE `/v2/groups/{tag}/events/{eventid}`

Delete an event.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.events.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `eventid` | path | string | Yes | The event's `id` |

### Example

```http
DELETE /v2/groups/mygroup/events/evt-2
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "Event deleted"
}
```

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.events.manage` (`You don't have permission to manage events`) |
| `404` | No event has that ID in this group (`Event not found`) |
