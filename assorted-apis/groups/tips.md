# Tips

Tips are credits given to a group. They go into the group's `credits_balance`, and members with `groups.tips.withdraw` can move credits from that balance to their own account.

## GET `/v2/groups/{tag}/tips`

List a group's tips, newest first.

**Auth:** Required. Sub-tokens need `groups:view`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `limit` | query | integer | No | Maximum number to return. Default `20`; invalid or non-positive values use the default |

### Example

```http
GET /v2/groups/mygroup/tips
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of [tip objects](README.md#tip), or `null` if there are none.

```json
[
  {
    "id": "tip-1",
    "group_tag": "mygroup",
    "from_username": "bob",
    "amount_credits": 25.0,
    "note": "keep it up",
    "created_at": 1717000000
  }
]
```

## POST `/v2/groups/{tag}/tips`

Send credits to a group. You can tip any public group, and private groups you're a member of. The amount is taken from your balance as a `group_tip` transaction.

**Auth:** Required. Sub-tokens need `credits:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `amount` | query | number | Yes | Credits to give. Must be positive |
| `note` | query | string | No | Up to 200 characters |

### Example

```http
POST /v2/groups/mygroup/tips?amount=10&note=thanks
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the new tip. Unlike the list endpoint, it has `from_user_id` (your user ID) instead of `from_username`.

```json
{
  "id": "tip-2",
  "group_tag": "mygroup",
  "from_user_id": "user-id-here",
  "amount_credits": 10.0,
  "note": "thanks",
  "created_at": 1717100000
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `amount` is missing, not a number, or not positive (`Invalid amount`) |
| `400` | `note` is over 200 characters (`Note length exceeded (max 200)`) |
| `400` | You don't have enough credits (`Insufficient funds`). The body also has `required` and `available` |
| `403` | The group is private and you aren't a member (`You can only tip groups you're a member of`) |

## POST `/v2/groups/{tag}/tips/withdraw`

Move credits from the group's `credits_balance` to your account, as a `group_tip_withdrawal` transaction.

**Auth:** Required. Sub-tokens need `credits:manage`. You need `groups.tips.withdraw`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `amount` | query | number | Yes | Credits to withdraw. Must be positive |

### Example

```http
POST /v2/groups/mygroup/tips/withdraw?amount=50
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:**

```json
{
  "id": "wd-1",
  "group_tag": "mygroup",
  "to_username": "alice",
  "amount_credits": 50.0,
  "created_at": 1717200000
}
```

### Errors

| Status | When |
| --- | --- |
| `400` | `amount` is missing, not a number, or not positive (`Invalid amount`) |
| `400` | The group's balance is too low (`Insufficient funds in group tip jar`). The body usually also has `required` and `available` |
| `403` | You lack `groups.tips.withdraw` (`You don't have permission to withdraw from the group tip jar`) |

## GET `/v2/groups/{tag}/tips/withdrawals`

List withdrawals from the group's balance, newest first.

**Auth:** Required. Sub-tokens need `groups:view`. You need `groups.tips.withdraw`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `limit` | query | integer | No | Maximum number to return. Default `20`; invalid or non-positive values use the default |

### Example

```http
GET /v2/groups/mygroup/tips/withdrawals
Authorization: Bearer YOUR_TOKEN
```

**Response `200`:** an array of withdrawals, or `null` if there are none.

```json
[
  {
    "id": "wd-1",
    "group_tag": "mygroup",
    "to_username": "alice",
    "amount_credits": 50.0,
    "created_at": 1717200000
  }
]
```

### Errors

| Status | When |
| --- | --- |
| `403` | You lack `groups.tips.withdraw` (`You don't have permission to view withdrawals`) |
