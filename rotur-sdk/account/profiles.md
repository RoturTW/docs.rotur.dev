# Profiles

`rotur.profiles` reads public user profiles and builds profile image URLs. Only `delete()` needs a token.

## rotur.profiles.get(username, includePosts?)

Gets a user's public profile.

**Auth:** None.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `username` | string | Yes | User to look up |
| `includePosts` | boolean | No | Include the user's posts. Default `true`. Pass `false` for a faster response |

```ts
const profile = await rotur.profiles.get("alice");
const withoutPosts = await rotur.profiles.get("alice", false);
```

**Returns:** `UserProfile` with `username`, `pfp`, `banner`, `background`, `profile_video`, `bio`, `pronouns`, `system`, `created`, `followers`, `following`, `currency`, `subscription`, `max_size`, `badges`, `theme`, `private`, `banned`, `status`, `posts`, `followed`, `follows_me`, `id`, and `index`. `subscription` is always the tier name as a string (`"Free"` when there is none).

## rotur.profiles.exists(username)

Checks whether a username is taken.

**Auth:** None.

```ts
const { exists } = await rotur.profiles.exists("alice");
```

**Returns:** `{ exists: boolean }`

## rotur.profiles.supporters()

Lists every user with a paid subscription.

**Auth:** None.

```ts
const supporters = await rotur.profiles.supporters();
// [{ username: "alice", subscription: "Plus" }, ...]
```

**Returns:** `Array<{ username, subscription }>`

## rotur.profiles.delete(username)

Deletes the signed-in user's own account.

{% hint style="danger" %}
This deletes the account. It cannot be undone from the SDK.
{% endhint %}

**Auth:** Required. Sub-tokens need `account:delete`.

```ts
await rotur.profiles.delete("alice");
```

**Returns:** `{ message }`

## Image URL helpers

These build URLs on `https://avatars.rotur.dev` without making a request. The optional second argument, `cache`, is appended as `?v=` so you can bust cached images. It defaults to an empty string.

| Method | URL |
| --- | --- |
| `rotur.profiles.getAvatarUrl(username, cache?)` | `https://avatars.rotur.dev/<username>?v=<cache>` |
| `rotur.profiles.getBannerUrl(username, cache?)` | `https://avatars.rotur.dev/.banners/<username>?v=<cache>` |
| `rotur.profiles.getOverlayUrl(username, cache?)` | `https://avatars.rotur.dev/.overlay/<username>?v=<cache>` |
| `rotur.profiles.getBackgroundUrl(username, cache?)` | `https://avatars.rotur.dev/.backgrounds/<username>?v=<cache>` |

`getProfileVideoUrl(username, cache?)` is a deprecated alias of `getBackgroundUrl()`.

**Auth:** None.

```ts
rotur.profiles.getAvatarUrl("alice");
// https://avatars.rotur.dev/alice?v=

rotur.profiles.getAvatarUrl("alice", "1234");
// https://avatars.rotur.dev/alice?v=1234
```

**Returns:** `string`
