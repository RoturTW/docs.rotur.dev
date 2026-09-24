# Search groups

Search public groups by tag, name, or description. Private groups never appear.

## GET `/v2/groups/search`

**Auth:** None.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `query` | query | string | No | Case-insensitive text to match anywhere in the tag, name, or description. Leave it out to list every public group |

### Example

```http
GET /v2/groups/search?query=gamedev
```

**Response `200`:** an array of [group objects](README.md#group), in no particular order. If nothing matches, the body is `null`, not `[]`.

```json
[
  {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "tag": "gamedev",
    "name": "Game Developers",
    "description": "A group for game devs",
    "readme": "",
    "rules": "",
    "icon_url": "",
    "banner_url": "",
    "owner_user_id": "bob",
    "public": true,
    "join_policy": "OPEN",
    "entry_fee": 0,
    "created_at": 1717000000,
    "credits_balance": 30.5,
    "member_count": 12
  }
]
```
