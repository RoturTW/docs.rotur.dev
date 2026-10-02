# Linking

`rotur.link` logs a user in with a link code, for apps that cannot open the browser login popup (CLIs, desktop apps, servers). Your app shows a code, the user enters it on rotur.dev and picks the permissions your app gets, and your app polls for the token. The token is always a scoped sub-token, never the account's own token, and it can never include `tokens:manage` or `account:delete`. Codes expire after 10 minutes. See [Authentication](../account/authentication.md) for the full flow.

{% hint style="warning" %}
The API answers `404` while a code is not linked yet, and the SDK throws `ApiError` for any error status. So `status()` and `linkedUser()` throw until the user has linked the code, and `pollUntilLinked()` stops on its first check instead of waiting. Until this is fixed, poll `linkedUser()` yourself and treat a `404` as "not linked yet".
{% endhint %}

## rotur.link.getCode()

Creates a new link code.

**Auth:** None.

```ts
const { code } = await rotur.link.getCode();
console.log("Enter this code on rotur.dev:", code);
```

**Returns:** `{ code, expires_in }`. `code` is six characters, such as `"A1B2C3"`, and `expires_in` is in seconds.

## rotur.link.status(code)

Gets the status of a link code.

**Auth:** None.

```ts
const { status } = await rotur.link.status(code);
// "linked"
```

**Returns:** `{ status: "linked" }`. Throws `ApiError` `404` while the code is not linked, or once it has expired.

## rotur.link.linkedUser(code)

Gets the token for a link code once a user has linked it. The token is handed out once: after this call succeeds, the code is used up.

**Auth:** None.

```ts
import { ApiError } from "rotur-sdk";

try {
  const { linked, token } = await rotur.link.linkedUser(code);
  if (linked && token) rotur.setToken(token);
} catch (err) {
  if (!(err instanceof ApiError && err.status === 404)) throw err;
  // Not linked yet. Try again shortly.
}
```

**Returns:** `{ linked: true, token }`. Throws `ApiError` `404` while the code is not linked.

## rotur.link.pollUntilLinked(code, intervalMs?, timeoutMs?)

Calls the same endpoint as `linkedUser()` until the code is linked, then sets the token on the client and returns it. See the warning above: it currently throws on the first check if the user has not linked the code yet.

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

{% hint style="warning" %}
The API needs the sub-token to give the device, but `linkCode()` only sends the code. It currently fails with `400` ("A scoped token is required to link a device"). Let users link codes on rotur.dev instead.
{% endhint %}

```ts
await rotur.link.linkCode("A1B2C3");
```

**Returns:** typed as `string`. The API answers `{ linked: true, token_id }` on success.
