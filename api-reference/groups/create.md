# Create a group

Create a group that you own. It costs 15 credits, and each account can own one group at a time.

## POST `/v2/groups`

**Auth:** Required. Sub-tokens need `groups:manage`. Your account must be in good standing and must not have group changes blocked by moderation.

All parameters go in the query string, not the body.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `tag` | query | string | Yes | Unique tag, 1–10 letters and digits |
| `name` | query | string | Yes | Display name, up to 50 characters |
| `description` | query | string | No | Up to 500 characters |
| `readme` | query | string | No | Up to 10,000 characters |
| `rules` | query | string | No | Up to 5,000 characters |
| `entry_fee` | query | number | No | Credits charged to join. Default `0` |
| `public` | query | string | No | `true` makes the group public. Anything else, or leaving it out, makes it private |
| `join_policy` | query | string | No | `OPEN`, `REQUEST`, or `INVITE`. Default `OPEN` |

### Example

```http
POST /v2/groups?tag=mygroup&name=My%20Group&public=true
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the new [group object](README.md#group).

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "",
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
  "member_count": 1
}
```

You become the first member, with the **Owner** and **Member** roles. The 15 credits are recorded as a `group_create` transaction on your account.

### Errors

| Status | When |
| --- | --- |
| `400` | `tag` is missing (`Tag is required`), longer than 10 characters (`Tag must be 10 characters or less`), or not letters and digits (`Tag must be alphanumeric only`) |
| `400` | `name` is missing (`Name is required`) or longer than 50 characters (`Name length exceeded`) |
| `400` | `description`, `readme`, or `rules` is too long (`Description length exceeded`, `Readme length exceeded`, `Rules length exceeded`) |
| `400` | `entry_fee` is negative or not a number (`Invalid entry fee`) |
| `400` | `join_policy` isn't one of the three values (`Invalid join policy`) |
| `400` | The tag is taken (`Group with this tag already exists`) |
| `400` | You already own a group (`You already own a group`) |
| `400` | You have fewer than 15 credits (`Insufficient funds to create a group (15 credits required)`). The body also has `required` and `available` |
| `403` | Your account standing is too low (`Your account standing does not allow this action. Current: <standing>`) |
| `403` | Moderation has blocked group changes on your account (`Group changes is blocked for this account`). The body also has `feature`, and `reason` and `expires_at` when set |
