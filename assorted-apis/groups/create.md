# Create a Group

### POST `/groups/create`

Creates a new group. **Costs 50 credits.** Each user can only own one group.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `tag` | string | Yes | Unique group tag (alphanumeric, max 20 chars) |
| `name` | string | Yes | Group display name (max 50 chars) |
| `description` | string | No | Group description (max 500 chars) |
| `readme` | string | No | Long-form group readme (max 10,000 chars) |
| `rules` | string | No | Group rules shown before joining (max 5,000 chars) |
| `entry_fee` | float | No | Credits required to join (default: `0`) |
| `public` | string | No | `"true"` or `"false"` (default: `"false"`) |
| `join_policy` | string | No | `"OPEN"`, `"REQUEST"`, or `"INVITE"` (default: `"OPEN"`) |

**Response (201):**

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
  "member_count": 1
}
```

The creator is automatically added as a member with the **Owner** role (all permissions) and the default **Member** role.

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Tag is required` | No tag provided |
| 400 | `Tag must be alphanumeric only` | Tag contains special characters |
| 400 | `Tag length exceeded` | Tag longer than 20 chars |
| 400 | `Name is required` | No name provided |
| 400 | `Name length exceeded` | Name longer than 50 chars |
| 400 | `Description length exceeded` | Description longer than 500 chars |
| 400 | `Readme length exceeded` | Readme longer than 10,000 chars |
| 400 | `Rules length exceeded` | Rules longer than 5,000 chars |
| 400 | `Invalid entry fee` | Entry fee is negative or not a valid number |
| 400 | `Invalid join policy` | Join policy not one of the valid values |
| 400 | `Group with this tag already exists` | Tag is taken |
| 400 | `You already own a group` | You already own a group |
| 400 | `Insufficient funds to create a group (50 credits required)` | Not enough credits |

**Transaction:** A `group_create` transaction for 50 credits is recorded on your account.
