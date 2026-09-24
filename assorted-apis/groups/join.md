# Join a group

Join a public group directly.

## POST `/v2/groups/{tag}/join`

**Auth:** Required. Sub-tokens need `groups:join`.

The group must be public. What happens depends on its `join_policy`:

| Policy | Result |
| --- | --- |
| `OPEN` | You join immediately |
| `INVITE` | You join only if you have a pending [invite](invites.md), which is marked accepted |
| `REQUEST` | Refused. Send a [join request](join-requests.md) instead |

If the group has an entry fee, it's taken from your balance and added to the group's `credits_balance`. If the group has rules, show them to the user before calling this endpoint.

You get every role marked `assign_on_join`. If no role is marked, you get the role named **Member**.

### Example

```http
POST /v2/groups/mygroup/join
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** the updated [group object](README.md#group).

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "A group for testing",
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

### Errors

| Status | When |
| --- | --- |
| `400` | You're already a member (`You are already a member of this group`) |
| `400` | The join policy is `REQUEST` (`This group requires a join request. Use the join request endpoint instead.`) |
| `400` | You can't afford the entry fee (`Insufficient funds to join this group`). The body also has `required` and `available` |
| `403` | The group is private (`Group is private`) |
| `403` | The join policy is `INVITE` and you have no pending invite (`This group is invite-only and you don't have a pending invite`) |
| `403` | You're banned from the group (`You are banned from this group`) |
