# Represent / Stop Representing a Group

## Represent a Group

### POST `/groups/{tag}/rep`

Set this group as your represented group on your Rotur profile. This sets `sys.group` on your account and causes `group_tag` to appear in your profile response.

You must be a member of the group.

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
  "message": "You are now representing this group"
}
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are not a member of this group` | Not a member |
| 404 | `Group not found` | Group doesn't exist |

---

## Stop Representing a Group

### POST `/groups/{tag}/disrep`

Removes your represented group from your profile.

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
  "message": "You are no longer representing any group"
}
```
