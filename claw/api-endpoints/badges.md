# GET `/badges`

Returns your badges and your badge display preferences.

**Auth:** Required. Sub-tokens need `account:view`.

Badges are worked out from your account each time: the system you signed up on, your credits, your number of friends, your subscription, account age and other activity, plus badges granted by hand. Some badges, such as `rich` and `friendly`, have levels and include your progress toward the next one.

### Example

```http
GET /badges
Authorization: Bearer <token>
```

**Response `200`:**

```json
{
  "badges": [
    {
      "id": "rich",
      "name": "rich",
      "icon": "c #DAF0F2 w 3 line 3 5 -3 5 ...",
      "description": "Awarded for having 1,000+ credits",
      "issuer": "rotur",
      "evolving": true,
      "level": 3,
      "progress": 1234.5,
      "next_threshold": 2500
    }
  ],
  "badge_names": [
    { "id": "rich", "name": "rich", "...": "same as badges" }
  ],
  "all_badges": [
    { "id": "rich", "name": "rich", "...": "same as badges", "hidden": false, "pinned": false }
  ],
  "preferences": {
    "hidden_badges": [],
    "badge_order": []
  }
}
```

| Field | Description |
| --- | --- |
| `badges` | Your visible badges, in display order |
| `badge_names` | Same as `badges`. Deprecated; use `badges` |
| `all_badges` | Every badge you have, including hidden ones, each with `hidden` and `pinned` flags |
| `preferences` | The IDs you have hidden (`hidden_badges`) and your custom order (`badge_order`) |

`icon` is a vector drawing string, not an image URL.

### Errors

| Status | When |
| --- | --- |
| `500` | Your badge preferences could not be loaded |
