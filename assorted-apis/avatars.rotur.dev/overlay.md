# .overlay

Get the avatar overlay a user has equipped from the [cosmetics shop](../cosmetics/README.md), to draw on top of their avatar.

> **Base URL:** `https://avatars.rotur.dev`
>
> **Auth:** None.

## GET `/.overlay/:username`

Returns the equipped overlay as a GIF. Also accepts `HEAD`. Also available at `/v2/avatars/:username/overlay`.

**Auth:** None.

It works like a profile picture, with `.overlay` in front of the username:

* https://avatars.rotur.dev/.overlay/mist
* https://avatars.rotur.dev/.overlay/MiSt

You can pass a 36-character user ID instead of a username. Responses carry an `ETag` and `Cache-Control: public, max-age=300, must-revalidate`.

If the user does not exist or has no overlay equipped, you get a 1×1 transparent PNG instead. You can always layer this URL over the avatar without checking first.

{% hint style="info" %}
This endpoint always returns `200` (or `304` for a matching `ETag`). A missing overlay is the transparent pixel, not an error.
{% endhint %}

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `username` | path | string | Yes | A username or 36-character user ID |

### Example

```http
GET /.overlay/mist
```

**Response `200`:** an `image/gif`, or a 1×1 transparent `image/png` when there is no overlay.
