# Check

Accessed via `rotur.check`. Provides bulk verification endpoints.

## Check Banned Users

Submit a list of usernames and get back which ones are banned:

```ts
const { banned } = await rotur.check.banned(["alice", "bob", "charlie"]);
// ["bob"]  — only bob is banned
```

This endpoint requires authentication.
