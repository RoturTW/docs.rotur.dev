# Tokens

`rotur.tokens` manages sub-tokens: tokens with a limited set of permissions that you give to third-party apps. Creating and changing sub-tokens needs your main token. For the HTTP endpoints, see [Tokens](../../assorted-apis/tokens/README.md).

Sub-tokens are returned as `SubTokenPublic`: `{ id, name, permissions, created_at, revoked, last_used_at?, expires_at?, revoked_at?, origin?, description?, websites?, token? }`.

## rotur.tokens.permissions()

Lists every permission a sub-token can have, and the named groups they are organized in.

**Auth:** None.

```ts
const { permissions, groups } = await rotur.tokens.permissions();
```

**Returns:** `{ permissions: string[], groups: Array<{ name, description, permissions }> }`

## rotur.tokens.list()

Lists all your sub-tokens.

**Auth:** Required. Sub-tokens need `tokens:manage`.

```ts
const { tokens, total } = await rotur.tokens.list();
```

**Returns:** `{ tokens: SubTokenPublic[], total }`

## rotur.tokens.active()

Lists your sub-tokens that are still active.

**Auth:** Required. Sub-tokens need `tokens:manage`.

```ts
const { tokens, total } = await rotur.tokens.active();
```

**Returns:** `{ tokens: SubTokenPublic[], total }`

## rotur.tokens.create(name, permissions, options?)

Creates a sub-token.

**Auth:** Required. Main token only.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | Yes | Token name |
| `permissions` | string[] | Yes | Permissions to grant, for example `["posts:view"]` |
| `options.expiresInHrs` | number | No | Hours until the token expires |
| `options.origin` | string | No | Origin of the app using the token |
| `options.description` | string | No | Description |
| `options.websites` | string[] | No | Websites of the app |

```ts
const result = await rotur.tokens.create("my-bot", ["posts:view", "posts:create"], {
  expiresInHrs: 24,
  origin: "https://myapp.com",
  description: "Bot access for my app",
  websites: ["https://myapp.com"],
});
console.log(result.token);
```

{% hint style="warning" %}
Store `token` from the response right away. It is only shown once.
{% endhint %}

**Returns:** `{ id, name, token, permissions, created_at, expires_at?, origin?, description?, websites? }`

## rotur.tokens.get(id)

Gets one sub-token.

**Auth:** Required. No specific permission.

```ts
const token = await rotur.tokens.get("token-id");
```

**Returns:** `SubTokenPublic`

## rotur.tokens.activity(id)

Gets a sub-token's status and usage.

**Auth:** Required. Sub-tokens need `tokens:manage`.

```ts
const activity = await rotur.tokens.activity("token-id");
console.log(activity.status, activity.last_used_at);
```

**Returns:** `{ id, name, status, permissions, created_at, last_used_at?, expires_at?, revoked_at?, origin?, description?, websites? }`. `status` is `"active"`, `"revoked"`, or `"expired"`.

## rotur.tokens.update(id, options)

Changes a sub-token's name, permissions, description, or websites.

**Auth:** Required. Main token only.

```ts
const updated = await rotur.tokens.update("token-id", {
  name: "renamed-bot",
  permissions: ["posts:view"],
  description: "Updated description",
  websites: ["https://newapp.com"],
});
```

**Returns:** `SubTokenPublic`

## rotur.tokens.rename(id, name)

Renames a sub-token.

**Auth:** Required. Main token only.

```ts
await rotur.tokens.rename("token-id", "new-name");
```

**Returns:** `{ message, id, name }`

## rotur.tokens.revoke(id)

Revokes a sub-token so it stops working.

**Auth:** Required. Main token only.

```ts
await rotur.tokens.revoke("token-id");
```

**Returns:** `{ message, id }`

## rotur.tokens.delete(id)

Deletes a sub-token.

**Auth:** Required. Main token only.

```ts
await rotur.tokens.delete("token-id");
```

**Returns:** `{ message, id }`
