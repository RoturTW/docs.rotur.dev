# Update a group

Change a group's settings. Only the fields you send are changed.

## PATCH `/v2/groups/{tag}`

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the owner or hold `groups.group.edit` or `groups.manage`. Moderation must not have blocked group changes on your account.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `name` | body | string | No | Display name, 1–50 characters after trimming spaces |
| `tag` | body | string | No | New tag, 1–10 letters and digits, not already in use. Renames the group |
| `description` | body | string | No | New description. No length limit is enforced here |
| `readme` | body | string | No | Up to 10,000 characters |
| `rules` | body | string | No | Up to 5,000 characters |
| `icon` or `icon_url` | body | string | No | Icon URL. To upload an image, use [the icon endpoint](icon.md) |
| `banner_url` | body | string | No | Banner URL. To upload an image, use [the banner endpoint](banner.md) |
| `public` | body | boolean | No | Whether the group is public |
| `join_policy` | body | string | No | `OPEN`, `REQUEST`, or `INVITE` |
| `entry_fee` | body | number | No | Credits charged to join. `0` removes the fee |

Fields with the wrong JSON type (for example `"public": "true"` as a string) are ignored.

### Example

```http
PATCH /v2/groups/mygroup
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "description": "Updated description", "entry_fee": 5 }
```

**Response `200`:** the updated [group object](README.md#group).

```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "tag": "mygroup",
  "name": "My Group",
  "description": "Updated description",
  "readme": "# Welcome",
  "rules": "1. Be kind",
  "icon_url": "",
  "banner_url": "",
  "owner_user_id": "alice",
  "public": true,
  "join_policy": "OPEN",
  "entry_fee": 5.0,
  "created_at": 1717000000,
  "credits_balance": 0,
  "member_count": 5
}
```

{% hint style="warning" %}
Changing `tag` renames the group immediately. Requests and links that use the old tag stop working.
{% endhint %}

### Errors

| Status | When |
| --- | --- |
| `400` | The body isn't valid JSON (`Invalid request body`) |
| `400` | `name` is empty (`Name cannot be empty`) or longer than 50 characters (`Name length exceeded`) |
| `400` | `readme` or `rules` is too long (`Readme length exceeded`, `Rules length exceeded`) |
| `400` | `entry_fee` is negative (`Entry fee cannot be negative`) |
| `400` | `join_policy` isn't one of the three values (`Invalid join policy`) |
| `400` | The new `tag` is empty, too long, not letters and digits, or taken (`Tag cannot be empty`, `Tag must be 10 characters or less`, `Tag must be alphanumeric only`, `Group with this tag already exists`) |
| `403` | You aren't the owner and lack an edit permission (`You are not authorized to update this group`) |
| `403` | Moderation has blocked group changes on your account (`Group changes is blocked for this account`) |
