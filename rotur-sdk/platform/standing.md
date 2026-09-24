# Standing

`rotur.standing` looks up an account's standing: the level that limits what the account can do after moderation action.

## rotur.standing.get(username)

Gets a user's standing and its history.

**Auth:** None.

```ts
const info = await rotur.standing.get("alice");
```

**Returns:** `StandingInfo`:

```ts
{
  username: "alice",
  standing: "warning",   // "good" | "warning" | "suspended" | "banned"
  recover_at: 0,         // timestamp when standing recovers (0 = never)
  history: [
    {
      level: "warning",
      previous: "good",  // optional
      reason: "Spamming",
      admin_id: "user-id", // optional
      timestamp: 1735689600,
    },
  ],
}
```

## Standing levels

| Level | Effect |
| --- | --- |
| `good` | Full access to all features |
| `warning` | Reduced trading and interaction |
| `suspended` | Severely limited; cannot create content |
| `banned` | Account disabled |

Standing recovers over time unless it is `banned`: `warning` returns to `good` after 7 days, and `suspended` returns to `warning` after 30 days.
