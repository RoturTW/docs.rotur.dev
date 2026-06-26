# avatars.rotur.dev

Rotur avatars are served from avatars.rotur.dev allowing a client to render a minimal rotur account ui with only the username.

### About

* Images are all stored as jpeg
* Images are 256x256
* Patreon supporters get 512x512 image uploads
* Images are cached for performance
* If the user has not set a profile picture, a default image is returned

### How do I get a profile picture?

Simply take the base api url "https://avatars.rotur.dev/" and add the rotur username on the end of it. The username is case insensitive, the same as rotur usernames generally are in all other apis.

* https://avatars.rotur.dev/mist
* https://avatars.rotur.dev/MiSt

### What if the user doesn't have a profile picture?

If the user does not have a profile picture set, a default image will be returned. This image is a simple silhouette of a person on a colored background.

### Query Parameters

You can add the following query parameters to customize the avatar:

* `s`: Specify the size of the avatar. Default is 256. Example: `?s=128`
* `radius`: Specify the border radius of the avatar. Default is 0 (square). Example: `?radius=128` for a circular avatar.
* `no_animate`: Set to `1` to force an animated (GIF) avatar to be served as a still image. The value must be exactly `1` — any other value is ignored. Example: `?no_animate=1`
