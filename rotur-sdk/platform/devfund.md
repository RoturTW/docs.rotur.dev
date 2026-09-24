# DevFund

`rotur.devfund` moves credits in and out of escrow for development petitions: backers pay into escrow, and the credits are later released to the developer.

## rotur.devfund.escrowTransfer(amount, petitionId, note?)

Moves credits from your balance into escrow for a petition.

**Auth:** Required. Sub-tokens need `credits:transfer`.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `amount` | number | Yes | Credits to put in escrow. Minimum 0.01 |
| `petitionId` | string | Yes | Petition to fund |
| `note` | string | No | Description, up to 50 characters |

```ts
const result = await rotur.devfund.escrowTransfer(100, "petition-123", "Funding the project");
```

**Returns:** `{ message, from, amount, petition_id, new_balance }`

## rotur.devfund.escrowRelease(amount, toUsername, petitionId, note?)

Releases escrowed credits to a developer. Admin only.

**Auth:** Required. Sub-tokens need `credits:manage`.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `amount` | number | Yes | Credits to release |
| `toUsername` | string | Yes | Developer to pay |
| `petitionId` | string | Yes | Petition being fulfilled |
| `note` | string | No | Description |

```ts
const result = await rotur.devfund.escrowRelease(100, "developer_name", "petition-123", "Milestone completed");
```

**Returns:** `{ message, to, amount, petition_id, new_balance }`

## rotur.devfund.escrowReleaseService(amount, toUsername, petitionId, apiKey, note?)

Releases escrowed credits using a DevFund service key instead of a user token. Use it from a trusted server.

**Auth:** No token. Sends `apiKey` in the `X-Devfund-Key` header.

{% hint style="danger" %}
Never ship the DevFund service key in client-side code.
{% endhint %}

```ts
const result = await rotur.devfund.escrowReleaseService(
  100,
  "developer_name",
  "petition-123",
  process.env.DEVFUND_KEY!,
  "Milestone completed",
);
```

**Returns:** `{ message, to, amount, petition_id, new_balance }`
