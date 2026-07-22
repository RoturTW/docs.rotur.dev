# .overlay

Get the avatar overlay a user currently has equipped from the [cosmetics shop](../cosmetics/README.md). Render it on top of their avatar.

### How do I get an overlay?

Same as a profile picture, but with `.overlay` in front of the username:

* https://avatars.rotur.dev/.overlay/mist
* https://avatars.rotur.dev/.overlay/MiSt

### About

* Returns the equipped overlay as a GIF (`image/gif`)
* If the user does not exist or has no overlay equipped, you get a 1x1 transparent PNG instead, so you can always layer this URL over the avatar without checking first
* Responses are cached (`ETag` and `Cache-Control: max-age=300`)
* `GET` and `HEAD` both work, and a user ID (36 characters) can be used instead of a username

{% hint style="info" %}
This endpoint always returns `200`. A "missing" overlay is just the transparent pixel.
{% endhint %}
