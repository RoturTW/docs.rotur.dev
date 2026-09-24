# Represent a group

Show a group on your Rotur profile. Representing a group sets `sys.group` on your account, and your profile then includes the group's tag as `group_tag`. You can represent one group at a time.

## PUT `/v2/groups/{tag}/represent`

Start representing a group you're a member of. This replaces any group you were representing.

**Auth:** Required. Sub-tokens need `account:settings`.

### Example

```http
PUT /v2/groups/mygroup/represent
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "You are now representing this group"
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | You aren't a member (`You are not a member of this group`) |

## DELETE `/v2/groups/{tag}/represent`

Stop representing whichever group you represent. The route needs a `{tag}`, but its value isn't checked, and this endpoint never returns `404`.

**Auth:** Required. Sub-tokens need `account:settings`.

### Example

```http
DELETE /v2/groups/mygroup/represent
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:**

```json
{
  "message": "You are no longer representing any group"
}
```
