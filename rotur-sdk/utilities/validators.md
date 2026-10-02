# Validators

`rotur.validators` creates and checks validators: strings that let another service confirm which Rotur user generated them, without seeing the user's token. A validator is bound to a key string your app chooses, and it stops working after about 5 minutes. For how validators work, see [Validators](../../accounts-and-tokens/validators.md).

## rotur.validators.generate(key)

Generates a validator for the signed-in user, bound to `key`.

**Auth:** Required. Sub-tokens need `validators:generate`.

```ts
const { validator } = await rotur.validators.generate("my-app-key");
```

**Returns:** `{ validator }`. Throws `ApiError` `403` when the account is restricted, or when a parent or carer has turned off access to the chat server the key names.

## rotur.validators.validate(validator, key)

Checks a validator against the key it was generated for. It needs no token, so you can call it from any service.

**Auth:** None.

{% hint style="warning" %}
The API reads the validator from a query parameter called `v`, but `validate()` sends it as `validator`. It currently fails with `ApiError` `400` ("Validator is required"). Until the SDK is fixed, call the endpoint yourself:

```ts
const params = new URLSearchParams({ v: validator, key: "my-app-key" });
const result = await fetch(`https://api.rotur.dev/v2/validators/verify?${params}`)
  .then((res) => res.json());
```
{% endhint %}

```ts
const result = await rotur.validators.validate(validator, "my-app-key");
// { valid: true, username: "alice", id: "user-id", minor: false }
// { valid: false, error: "Validator expired or not found" }
```

**Returns:** `{ valid, username?, id?, error? }`. A valid result also has `minor`, and may have `restrictions`, `account_type`, and `owner` for sub accounts. The endpoint answers `403` when the account is restricted and `404` when the user does not exist.
