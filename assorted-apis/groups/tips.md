# Tips

Tips are credit donations to a group. They land in the group's `credits_balance`, which members with the right permission can withdraw from.

Users with the `groups.tips.withdraw` permission can withdraw credits from the group's tip jar to their own balance. Withdrawals are stored in a separate `withdrawals.json` file per group.

---

## List Tips

### GET `/v2/groups/{tag}/tips`

**Auth:** required. Token permission: `groups:view`.

Returns tips, newest first.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `limit` | int | No | Max number of results (default: 20) |

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/tips?auth=YOUR_TOKEN"
```

**Example response (200):**

```json
[
  {
    "id": "tip-1",
    "group_tag": "mygroup",
    "from_username": "bob",
    "amount_credits": 25.0,
    "note": "keep it up!",
    "created_at": 1717000000
  }
]
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 404 | `Group not found` | Group doesn't exist |

***

## Send a Tip

### POST `/v2/groups/{tag}/tips`

**Auth:** required. Token permission: `credits:manage`.

Send credits to a group. You must be a member, unless the group is public. The amount is deducted from your balance and recorded as a `group_tip` transaction.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `amount` | float | Yes | Credits to tip (must be positive) |
| `note` | string | No | A note with the tip (max 200 chars) |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/tips?auth=YOUR_TOKEN&amount=10&note=thanks"
```

**Example response (201):**

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

{% hint style="info" %}
The create response contains your raw `from_user_id`. The list endpoint returns `from_username` instead.
{% endhint %}

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Invalid amount` | Amount is not a positive number |
| 400 | `Note length exceeded (max 200)` | Note too long |
| 400 | `Insufficient funds` | Not enough credits (response includes `required` and `available`) |
| 403 | `You can only tip groups you're a member of` | Not a member and the group is private |
| 404 | `Group not found` | Group doesn't exist |

***

## Withdraw from the Tip Jar

### POST `/v2/groups/{tag}/tips/withdraw`

**Auth:** required. Token permission: `credits:manage`. Requires the `groups.tips.withdraw` group permission.

Moves credits from the group's `credits_balance` to your account. Recorded as a `group_tip_withdrawal` transaction.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `amount` | float | Yes | Credits to withdraw (must be positive) |

**Example request:**

```bash
curl -X POST "https://api.rotur.dev/v2/groups/mygroup/tips/withdraw?auth=YOUR_TOKEN&amount=50"
```

**Example response (201):**

```json
{
  "id": "wd-1",
  "group_tag": "mygroup",
  "to_username": "alice",
  "amount_credits": 50.0,
  "created_at": 1717200000
}
```

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Invalid amount` | Amount is not a positive number |
| 400 | `Insufficient funds in group tip jar` | Group balance too low (response includes `required` and `available`) |
| 403 | `You don't have permission to withdraw from the group tip jar` | Missing `groups.tips.withdraw` |
| 404 | `Group not found` | Group doesn't exist |

***

## List Withdrawals

### GET `/v2/groups/{tag}/tips/withdrawals`

**Auth:** required. Token permission: `groups:view`. Requires the `groups.tips.withdraw` group permission.

Returns withdrawals, newest first.

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `limit` | int | No | Max number of results (default: 20) |

**Example request:**

```bash
curl "https://api.rotur.dev/v2/groups/mygroup/tips/withdrawals?auth=YOUR_TOKEN"
```

**Example response (200):**

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

**Common errors:**

| Status | Error | Cause |
|--------|-------|-------|
| 403 | `You don't have permission to view withdrawals` | Missing `groups.tips.withdraw` |
| 404 | `Group not found` | Group doesn't exist |

---

## Withdraw from Tip Jar

### POST `/groups/{tag}/tips/withdraw`

Withdraw credits from the group's tip jar to your own balance. You must have the `groups.tips.withdraw` permission in the group. The amount is deducted from the group's `credits_balance` and added to your balance.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `amount` | float | Yes | Amount of credits to withdraw (must be positive) |

**Response (201):**

```json
{
  "id": "withdrawal-1",
  "group_tag": "mygroup",
  "to_username": "alice",
  "amount_credits": 50.0,
  "created_at": 1717200000
}
```

**Transaction:** A `group_tip_withdrawal` transaction is recorded on your account for the withdrawn amount. The group's `credits_balance` is reduced by the withdrawal amount.

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Group tag is required` | No tag provided |
| 400 | `Invalid amount` | Amount is not a positive number |
| 400 | `Insufficient funds in group tip jar` | Group doesn't have enough credits |
| 403 | `You don't have permission to withdraw from the group tip jar` | Missing `groups.tips.withdraw` permission |
| 404 | `Group not found` | Group doesn't exist |

---

## List Withdrawals

### GET `/groups/{tag}/tips/withdrawals`

Returns withdrawals from the group's tip jar, newest first. You must have the `groups.tips.withdraw` permission in the group.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `limit` | int | No | Max number of results (default: 20) |

**Response (200):**

```json
[
  {
    "id": "withdrawal-1",
    "group_tag": "mygroup",
    "to_username": "alice",
    "amount_credits": 50.0,
    "created_at": 1717200000
  }
]
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Group tag is required` | No tag provided |
| 403 | `You don't have permission to view withdrawals` | Missing `groups.tips.withdraw` permission |
| 404 | `Group not found` | Group doesn't exist |
