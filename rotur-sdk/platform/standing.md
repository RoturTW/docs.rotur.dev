# Standing

Accessed via `rotur.standing`. Standing represents a user's reputation level on the platform.

## Get a User's Standing

```ts
const info = await rotur.standing.get("alice");
```

Returns a `StandingInfo`:

```ts
{
  username: "alice",
  standing: "good",        // "good" | "warning" | "suspended" | "banned"
  recover_at: 0,           // unix timestamp when standing auto-recovers (0 = never)
  history: [
    {
      level: "warning",
      reason: "Spamming",
      set_by: "admin-id",
      set_at: 1735689600,
    },
  ],
}
```

This endpoint is public (no auth required).

## Standing Levels

| Level | Effect |
|-------|--------|
| `good` | Full access to all features |
| `warning` | Reduced trading/interaction ability |
| `suspended` | Severely limited, cannot create content |
| `banned` | Account disabled |

Standing automatically recovers over time (warning → good after 7 days, suspended → warning after 30 days) unless set to `banned`.
