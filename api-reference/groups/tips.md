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

## Fundraising campaigns

A campaign is a fundraising goal inside a group. Contributions go into the group's `credits_balance` like any other tip, and also show up in the tip list with a `campaign_id`. A group can have one pinned campaign at a time.

Campaign `status` is `ACTIVE`, `COMPLETED` (the goal has been reached), or `CLOSED`. Timestamps are in Unix seconds, and `created_by` is a username. `contributor_count` counts contributions, so someone who gives twice is counted twice.

```json
{
  "id": "camp-1",
  "group_tag": "mygroup",
  "title": "New server",
  "description": "Help us pay for hosting",
  "goal_credits": 500.0,
  "raised_credits": 120.0,
  "contributor_count": 4,
  "created_by": "alice",
  "created_at": 1717000000,
  "deadline": 1719600000,
  "status": "ACTIVE",
  "pinned": true
}
```

`deadline` is left out when the campaign has none.

## GET `/v2/groups/{tag}/campaigns`

List a group's campaigns, pinned first, then newest first.

**Auth:** None for public groups. For a private group, send a token for an account that's a member.

### Example

```http
GET /v2/groups/mygroup/campaigns
```

**Response `200`:** an array of campaign objects, or `[]` if there are none.

### Errors

| Status | When |
| --- | --- |
| `403` | The group is private and you aren't a member (`This group is private`) |

## POST `/v2/groups/{tag}/campaigns`

Start a campaign.

**Auth:** Required. Sub-tokens need `groups:manage`. You need `groups.tips.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `title` | query | string | Yes | Up to 100 characters |
| `description` | query | string | No | Up to 1,000 characters |
| `goal_credits` | query | number | Yes | The goal in credits. Must be positive |
| `deadline` | query | integer | No | When the campaign stops taking contributions, as a future Unix timestamp in seconds |
| `pinned` | query | string | No | Default `true`, which unpins any other campaign. Send `false` to leave it unpinned |

### Example

```http
POST /v2/groups/mygroup/campaigns?title=New%20server&goal_credits=500
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the new campaign object.

### Errors

| Status | When |
| --- | --- |
| `400` | `title` is missing (`Title is required`) or over 100 characters (`Title length exceeded`) |
| `400` | `description` is over 1,000 characters (`Description length exceeded`) |
| `400` | `goal_credits` is missing, not a number, or not positive (`Enter a valid fundraising goal`) |
| `400` | `deadline` isn't a future timestamp (`Deadline must be in the future`) |
| `403` | You lack `groups.tips.manage` (`You don't have permission to manage fundraising`) |

## PATCH `/v2/groups/{tag}/campaigns/{campaignid}`

Update a campaign. Only the fields you send are changed.

**Auth:** Required. Sub-tokens need `groups:manage`. You must be the owner or hold `groups.tips.manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `campaignid` | path | string | Yes | The campaign's `id` |
| `title` | body | string | No | 1–100 characters |
| `description` | body | string | No | Up to 1,000 characters |
| `deadline` | body | integer | No | Unix timestamp in seconds. `0` removes the deadline |
| `status` | body | string | No | `ACTIVE` or `CLOSED`. A reopened campaign that has already reached its goal becomes `COMPLETED` |
| `pinned` | body | boolean | No | `true` pins it and unpins any other campaign |

### Example

```http
PATCH /v2/groups/mygroup/campaigns/camp-1
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{ "status": "CLOSED" }
```

**Response `200`:** the updated campaign object.

### Errors

| Status | When |
| --- | --- |
| `400` | The body isn't valid JSON, or a field has the wrong type (`Invalid request body`) |
| `400` | `title` is empty or over 100 characters (`Title must be between 1 and 100 characters`) |
| `400` | `description` is over 1,000 characters (`Description length exceeded`) |
| `400` | `status` isn't `ACTIVE` or `CLOSED` (`Status must be ACTIVE or CLOSED`) |
| `403` | You aren't the owner and lack `groups.tips.manage` (`You don't have permission to manage fundraising`) |
| `404` | No campaign has that ID in this group (`Fundraiser not found`) |

## POST `/v2/groups/{tag}/campaigns/{campaignid}/contribute`

Give credits to a campaign. You can contribute to campaigns in any public group, and in private groups you're a member of. The amount is taken from your balance as a `group_tip` transaction. When the total reaches the goal, the campaign becomes `COMPLETED` and stops taking contributions.

**Auth:** Required. Sub-tokens need `credits:manage`.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `campaignid` | path | string | Yes | The campaign's `id` |
| `amount` | query | number | Yes | Credits to give. Must be positive |
| `note` | query | string | No | Up to 200 characters |

### Example

```http
POST /v2/groups/mygroup/campaigns/camp-1/contribute?amount=25
Authorization: Bearer YOUR_TOKEN
```

**Response `201`:** the updated campaign, and your contribution as a [tip object](README.md#tip) with `campaign_id` set.

```json
{
  "campaign": {
    "id": "camp-1",
    "group_tag": "mygroup",
    "title": "New server",
    "raised_credits": 145.0,
    "contributor_count": 5,
    "status": "ACTIVE"
  },
  "contribution": {
    "id": "tip-3",
    "group_tag": "mygroup",
    "from_username": "bob",
    "amount_credits": 25.0,
    "note": "",
    "created_at": 1717100000,
    "campaign_id": "camp-1"
  }
}
```

`campaign` is the full campaign object, shortened here.

### Errors

| Status | When |
| --- | --- |
| `400` | `amount` is missing, not a number, or not positive (`Enter a valid contribution amount`) |
| `400` | `note` is over 200 characters (`Note length exceeded`) |
| `400` | You don't have enough credits (`Insufficient funds`). The body also has `required` and `available` |
| `400` | The campaign is closed, completed, or past its deadline (`This fundraiser is no longer accepting contributions`) |
| `403` | The group is private and you aren't a member (`You must be a member to support this group`) |
| `404` | No campaign has that ID in this group (`Fundraiser not found`) |
