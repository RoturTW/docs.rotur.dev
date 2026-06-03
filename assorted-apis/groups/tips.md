# Tips

Tips are credit donations sent to a group. The total of all tips is reflected in the group's `credits_balance`. Tips are stored in a separate `tips.json` file per group.

## List Tips

### GET `/groups/{tag}/tips`

Returns tips for a group, newest first.

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
    "id": "tip-1",
    "group_tag": "mygroup",
    "from_username": "bob",
    "amount_credits": 25.0,
    "created_at": 1717000000
  }
]
```

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Group tag is required` | No tag provided |
| 404 | `Group not found` | Group doesn't exist |

---

## Send a Tip

### POST `/groups/{tag}/tips`

Send credits to a group. You must be a member (or the group must be public). The amount is deducted from your balance.

**Path Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `tag` | string | Yes | The group tag |

**Query Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `auth` | string | Yes | Your Rotur user token |
| `amount` | float | Yes | Amount of credits to tip (must be positive) |

**Response (201):**

```json
{
  "id": "tip-2",
  "group_tag": "mygroup",
  "from_username": "charlie",
  "amount_credits": 10.0,
  "created_at": 1717100000
}
```

**Transaction:** A `group_tip` transaction is recorded on your account for the tipped amount.

**Error Responses:**

| Status | Error | Cause |
|--------|-------|-------|
| 400 | `Invalid amount` | Amount is not a positive number |
| 400 | `Insufficient funds` | You don't have enough credits |
| 403 | `You can only tip groups you're a member of` | Not a member and group is private |
| 404 | `Group not found` | Group doesn't exist |
