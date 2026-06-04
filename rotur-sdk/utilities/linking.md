# Linking

Accessed via `rotur.link`. Link codes provide an auth flow for non-browser contexts.

## Get a Link Code

```ts
const { code } = await rotur.link.getCode();
// "ABCD-1234"
```

Display this code and have the user enter it at `rotur.dev`.

## Check Link Status

```ts
const { status } = await rotur.link.status("ABCD-1234");
```

## Get Linked User

```ts
const { linked, token } = await rotur.link.linkedUser("ABCD-1234");
if (linked && token) {
  rotur.setToken(token);
}
```

## Link Code (Authenticated)

If you're already authenticated and want to associate a code with the current user:

```ts
await rotur.link.linkCode("ABCD-1234");
```

## Poll Until Linked

Convenience method that polls `linkedUser` until the code is linked:

```ts
const token = await rotur.link.pollUntilLinked(
  "ABCD-1234",
  1500,     // poll every 1.5s
  120_000,  // timeout after 2 minutes
);
```

Throws an error on timeout.
