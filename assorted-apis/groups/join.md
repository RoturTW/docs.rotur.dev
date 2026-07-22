# Join a Group

Join a public group directly.

### POST `/v2/groups/{tag}/join`

**Auth:** required. Token permission: `groups:join`.

The group must be public. How the join works depends on the group's `join_policy`:

* `OPEN`: you join immediately.
* `INVITE`: you can only join if you have a pending [invite](invites.md). Joining accepts the invite.
* `REQUEST`: this endpoint refuses. Send a [join request](join-requests.md) instead.

If the group has an **entry fee**, the credits are deducted from your balance and added to the group's `credits_balance`. If the group has **rules**, show them and get agreement before calling this endpoint.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/join?auth=YOUR_TOKEN"
```

**Example response (200):**

Returns the updated group info:

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
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

You are automatically given every role marked `assign_on_join` (the default **Member** role if none are).

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `You are already a member of this group` | Already joined |
| 400 | `This group requires a join request. Use the join request endpoint instead.` | Join policy is `REQUEST` |
| 400 | `Insufficient funds to join this group` | Not enough credits for the entry fee (response includes `required` and `available`) |
| 403 | `Group is private` | Group is not public |
| 403 | `This group is invite-only and you don't have a pending invite` | Join policy is `INVITE` and no invite exists |
| 403 | `You are banned from this group` | You're on the group's ban list |
| 404 | `Group not found` | Group doesn't exist |
