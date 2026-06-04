# Validators

Accessed via `rotur.validators`. Validators are one-time tokens that prove a user owns a key.

## Generate a Validator

```ts
const { validator } = await rotur.validators.generate("key-id");
// "abc123def456..."
```

## Validate a Validator

```ts
const result = await rotur.validators.validate("validator-token", "key-id");
// { valid: true, username: "alice", id: "user-id" }
// { valid: false, error: "invalid" }
```

This endpoint is public (no auth required), making it safe to call from client apps.
