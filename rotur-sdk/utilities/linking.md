# Linking

`rotur.link` logs a user in with a link code, for apps that cannot open the browser login popup (CLIs, desktop apps, servers). Your app shows a code, the user enters it on rotur.dev, and your app polls for the token. See [Authentication](../account/authentication.md) for the full flow.

## rotur.link.getCode()

Creates a new link code.

**Auth:** None.

```ts
const { code } = await rotur.link.getCode();
console.log("Enter this code on rotur.dev:", code);
```

**Returns:** `{ code }`

## rotur.link.status(code)

Gets the status of a link code.

**Auth:** None.

```ts
const { status } = await rotur.link.status(code);
```

**Returns:** `{ status }`

## rotur.link.linkedUser(code)

Gets the token for a link code once a user has linked it.

**Auth:** None.

```ts
const { linked, token } = await rotur.link.linkedUser(code);
if (linked && token) rotur.setToken(token);
```

**Returns:** `{ linked, token? }`

## rotur.link.pollUntilLinked(code, intervalMs?, timeoutMs?)

Calls `linkedUser()` until the code is linked, then sets the token on the client and returns it.

**Auth:** None.

| Name | Type | Default | Description |
| --- | --- | --- | --- |
| `code` | string | | Link code |
| `intervalMs` | number | `1500` | Time between checks |
| `timeoutMs` | number | `120000` | Time before giving up |

```ts
const token = await rotur.link.pollUntilLinked(code, 1500, 120_000);
```

**Returns:** the token. Throws `Error("Link polling timed out")` on timeout.

## rotur.link.linkCode(code)

Links a code to the signed-in user, the step a user normally does on rotur.dev.

**Auth:** Required. Sub-tokens need `account:settings`.

```ts
await rotur.link.linkCode("ABCD-1234");
```

**Returns:** the response as a `string`.
