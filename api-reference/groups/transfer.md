# Transfer ownership

Hand your group to another member. Only the current owner can do this.

## POST `/v2/groups/{tag}/transfer/{userid}`

The new owner gets the **Owner** role and becomes the group's owner. You lose the Owner role but keep your other roles; if that leaves you with none, you get the default join roles. The new owner receives a `group_ownership_transferred` event and a push notification.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the group owner.

{% hint style="warning" %}
The transfer takes effect immediately. Only the new owner can transfer the group back.
{% endhint %}

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `userid` | path | string | Yes | The new owner's user ID. A username doesn't work here. They must already be a member |

### Example

```http
POST /v2/groups/mygroup/transfer/USER_ID
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** `group` is the full updated [group object](README.md#group), shortened here.

```json
{
  "message": "Ownership transferred",
  "group": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "tag": "mygroup",
    "name": "My Group",
    "owner_user_id": "bob",
    "member_count": 5
  }
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `userid` is your own ID (`You are already the owner`) |
| `403` | You aren't the owner (`Only the group owner can transfer ownership`) |
| `404` | The user isn't a member (`Target user is not a member of this group`) |
