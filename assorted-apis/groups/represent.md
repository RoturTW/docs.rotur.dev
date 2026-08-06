# Represent a Group

Show a group on your Rotur profile. Representing sets `sys.group` on your account, and the group's tag appears as `group_tag` in your profile response.

## Represent a Group

### PUT `/v2/groups/{tag}/represent`

**Auth:** required. Token permission: `account:settings`. You must be a member of the group.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Example request:**

```bash
curl -X PUT -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/represent"
```

**Example response (200):**

```json
{
  "message": "You are now representing this group"
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are not a member of this group` | Not a member |
| 404 | `Group not found` | Group doesn't exist |

***

## Stop Representing

### DELETE `/v2/groups/{tag}/represent`

**Auth:** required. Token permission: `account:settings`.

Removes your represented group, whichever group it was. The `tag` in the path is required by the route but not checked.

**Example request:**

```bash
curl -X DELETE -H "Authorization: Bearer YOUR_TOKEN" "https://api.rotur.dev/v2/groups/mygroup/represent"
```

**Example response (200):**

```json
{
  "message": "You are no longer representing any group"
}
```
