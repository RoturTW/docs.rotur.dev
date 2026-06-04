# DevFund

Accessed via `rotur.devfund`. The DevFund provides escrow-based credit transfers for development petitions.

## Escrow Transfer

Move credits from your account into escrow for a petition:

```ts
const result = await rotur.devfund.escrowTransfer(100, "petition-123", "Funding the project");
// { message, from, amount, petition_id, new_balance }
```

Parameters:
- `amount` — credits to transfer (minimum 0.01)
- `petitionId` — the petition to fund
- `note` — optional description (max 50 chars)

## Escrow Release (Admin Only)

Release escrowed credits to a developer:

```ts
const result = await rotur.devfund.escrowRelease(100, "developer_name", "petition-123", "Milestone completed");
// { message, to, amount, petition_id, new_balance }
```

Parameters:
- `amount` — credits to release
- `toUsername` — the developer to pay
- `petitionId` — the petition being fulfilled
- `note` — optional description
