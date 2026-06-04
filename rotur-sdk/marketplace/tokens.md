# Tokens

Accessed via `rotur.tokens`. Tokens (sub-tokens) let you grant limited API access to third-party apps.

## List Permissions

```ts
const { permissions, groups } = await rotur.tokens.permissions();
```

## List All Tokens

```ts
const { tokens, total } = await rotur.tokens.list();
```

## List Active Tokens

```ts
const { tokens, total } = await rotur.tokens.active();
```

## Create a Token

```ts
const result = await rotur.tokens.create("my-bot", ["posts:view", "posts:create"], {
  expiresInHrs: 24,
  origin: "https://myapp.com",
  description: "Bot access for my app",
  websites: ["https://myapp.com"],
});
console.log(result.token); // save this — it's shown only once
```

## Get a Token

```ts
const token = await rotur.tokens.get("token-id");
```

## Token Activity

```ts
const activity = await rotur.tokens.activity("token-id");
```

## Update a Token

```ts
const updated = await rotur.tokens.update("token-id", {
  name: "renamed-bot",
  permissions: ["posts:view"],
  description: "Updated desc",
  websites: ["https://newapp.com"],
});
```

## Rename a Token

```ts
await rotur.tokens.rename("token-id", "new-name");
```

## Revoke a Token

```ts
await rotur.tokens.revoke("token-id");
```

## Delete a Token

```ts
await rotur.tokens.delete("token-id");
```
