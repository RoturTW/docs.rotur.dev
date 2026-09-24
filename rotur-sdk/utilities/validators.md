# Validators

`rotur.validators` creates and checks validators: strings that let another service confirm which Rotur user generated them, without seeing the user's token. A validator is bound to a key string your app chooses. For how validators work, see [Validators](../../assorted-apis/validators/README.md).

## rotur.validators.generate(key)

Generates a validator for the signed-in user, bound to `key`.

**Auth:** Required. Sub-tokens need `validators:generate`.

```ts
const { validator } = await rotur.validators.generate("my-app-key");
```

**Returns:** `{ validator }`

## rotur.validators.validate(validator, key)

Checks a validator against the key it was generated for. It needs no token, so you can call it from any service.

**Auth:** None.

```ts
const result = await rotur.validators.validate(validator, "my-app-key");
// { valid: true, username: "alice", id: "user-id" }
// { valid: false, error: "invalid" }
```

**Returns:** `{ valid, username?, id?, error? }`
