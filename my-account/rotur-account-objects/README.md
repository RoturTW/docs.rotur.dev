# Rotur Account Objects

Your Rotur account is a simple JSON object of keys and values. Some keys are managed by the server, and some you can change yourself.

## Key Limits

* Key names must be 20 characters or fewer
* Key values must be 1,000 characters or fewer
* The whole account object is limited to 25,000 characters of text

{% hint style="info" %}
Keys starting with `sys.` are managed by the server. You cannot write to them directly. The keys `last_login`, `max_size`, `key`, `created`, `discord_id`, `sys.id` and `password` are locked and can never be updated by you.
{% endhint %}

## Read Only Keys

```
key
- your account token. Keep it secret, it is your login.

created
- the timestamp of when your account was created, in ms
  eg. 1712792411288

max_size
- the maximum size of your cloud file storage in characters of text.
  Set automatically from your subscription tier.
  eg. "5000000" (5 MB on the Free tier)

discord_id
- the id of the discord account linked to this rotur account.
  Set through roturBOT linking, you cannot edit it directly.
  eg. "603952506330021898"

sys.currency
- the number of rotur credits you have
> your credits cannot go negative and amounts are rounded to 2 decimal places
  eg. 14.35

sys.friends
- Array of rotur usernames that are your friends
  eg. ["throwaway", "rm"]

sys.requests
- Array of rotur usernames that have asked to be your friend
  eg. ["temp"]

sys.badges
- Array of your badges, recalculated every time you log in.
  Each badge has a name, an icon and a description.

sys.purchases
- Array of item ids that you own
  eg. ["c4068074d5ed5bfcae9a91874383dab9"]

sys.total_logins
- the number of times you have logged into rotur
  eg. 106

sys.transactions
- Array of your recent credit transactions. Each entry is an object with
  a type, amount, note, the other user, a timestamp and your new total.
  How many are kept depends on your subscription tier.

sys.notes
- your private notes about other users (Plus tier and above).
  See the Friend Notes page.

sys.subscription
- your subscription state: tier, active flag and next billing timestamp

sys.social_links
- up to 3 social links shown on your profile
```

## Writable Keys

```
username
- your account's username (case insensitive). You can change it as long
  as the new name is valid and not already taken.
  eg. mist

email
- your account email. Changing it marks your email as unverified and
  sends you a new verification email.

private
- whether to hide your account publicly (boolean)
  eg. true

bio
- your profile bio. Maximum length depends on your subscription tier
  (200 characters on Free, up to 1,000 on Pro).

pronouns
- shown on your profile
  eg. "she/her"

pfp
- special key. Set it to a data URI of an image and the server uploads it
  to our avatar host. Your avatar is then always available at
  https://avatars.rotur.dev/your_username
> animated GIF avatars need a Plus subscription or higher

banner
- special key. Set it to a data URI to upload a profile banner.
> each upload costs 10 credits unless you have a Pro subscription,
  and animated banners need Pro

system
- special key. Setting it switches your account to another registered
  rotur system. The value must match an existing system's name.

theme
- an object of colours, useful for matching your ui to the user's preference
  eg. {
    "primary": "#111111",
    "secondary": "#333333",
    "tertiary": "#555555",
    "text": "#ffffff",
    "background": "#000000",
    "accent": "#57cdac"
  }

wallpaper
- a url that should be used as the user's desktop wallpaper
> pexels is a good source for these since they don't enforce cors
  eg. https://images.pexels.com/photos/1612351/pexels-photo-1612351.jpeg
```
