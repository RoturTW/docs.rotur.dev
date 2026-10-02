# Standing

`rotur.standing` looks up an account's standing: the level that limits what the account can do after moderation action.

## rotur.standing.get(username)

Gets a user's standing. How much you get back depends on who is asking.

**Auth:** None. If the client has a token, it is sent.

| Caller | Gets |
| --- | --- |
| No token, a sub-token, or another user | `{ username, banned }` only |
| The account itself, with its main token | The full standing, below |

```ts
const info = await rotur.standing.get("alice");
```

**Returns:** for the account itself, `StandingInfo` plus `banned`:

```ts
{
  username: "alice",
  banned: false,
  standing: "warning",   // "good" | "warning" | "suspended" | "banned"
  recover_at: 1736294400, // timestamp when standing recovers (0 = no recovery set)
  history: [
    {
      level: "warning",
      previous: "good",  // optional
      reason: "Spamming",
      timestamp: 1735689600,
    },
  ],
}
```

History never says which member of staff made a change. The SDK types the result as `StandingInfo`, so check for `standing` before you read it, as other callers only get `{ username, banned }`.

## Standing levels

| Level | Effect |
| --- | --- |
| `good` | Full access to all features |
| `warning` | Reduced trading and interaction |
| `suspended` | Severely limited; cannot create content |
| `banned` | Account disabled |

Standing recovers over time unless it is `banned`. By default, `warning` returns to `good` after 7 days, and `suspended` returns to `warning` after 30 days. Rotur staff can set a different recovery time.
