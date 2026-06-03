# Join a Group

### POST `/groups/{tag}/join`

Join a public group. The group must be public and have an `OPEN` join policy. If the group has an **entry fee**, the required credits are deducted from your balance and added to the group's `credits_balance`. If the group has **rules**, the client should present them and require agreement before calling this endpoint.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |

**Response (200):**

Returns the updated group info with incremented `member_count`:

```json
{
  "tag": "mygroup",
  "name": "My Group",
  "description": "A cool group",
  "readme": "",
  "rules": "",
  "icon_url": "",
  "banner_url": "",
  "owner_user_id": "alice",
  "public": true,
  "join_policy": "OPEN",
  "entry_fee": 0,
  "created_at": 1717000000,
  "credits_balance": 0,
  "member_count": 6
}
```

You are automatically assigned the default **Member** role (the role with `assign_on_join: true`).

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are already a member of this group` | Already joined |
| 403 | `Group is private` | Group is not public |
| 403 | `This group is invite-only` | Join policy is `INVITE` |
| 400 | `Join requests not yet implemented` | Join policy is `REQUEST` |
| 400 | `Insufficient funds to join this group` | Not enough credits for the entry fee |
| 404 | `Group not found` | Group doesn't exist |
