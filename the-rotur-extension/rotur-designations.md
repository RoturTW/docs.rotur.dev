# Designations

A designation is a short name for your app, such as `rtr` or `ori`. You set it in `connect to server with designation: [rtr] ...` (see [Connect to Rotur](connecting-to-rotur.md)), and your client joins a room with that name when it connects. Users who share a designation can see each other, and mail and synced variables are sent on it.

The extension only tracks the members of your own designation. Blocks that take a `DESIGNATION` input return an empty result for any other designation.

## Who is connected

| Block | Type | Returns |
| --- | --- | --- |
| `(connected users)` | Reporter | JSON array of usernames connected on your designation |
| `(get all users on designation: [rtr])` | Reporter | JSON array of usernames on that designation. `[]` if it isn't your designation. |
| `<user [user] connected on designation: [rtr]>` | Boolean | `true` if the user is connected on that designation. `false` if it isn't your designation. |
| `<username [user] connected on any designation>` | Boolean | `true` if the user is connected on your designation |
| `(find all connections of username: [user])` | Reporter | JSON array of the member entries for that username on your designation, including their `username` and `user_id` |

{% hint style="info" %}
Despite its name, `username [user] connected on any designation` only checks your own designation.
{% endhint %}

## Joins and leaves

| Block | Type | Description |
| --- | --- | --- |
| `when a user connects` | Hat | Fires when someone joins your designation |
| `when a user disconnects` | Hat | Fires when someone leaves your designation |
| `(last user to join)` | Reporter | Username of the last user who joined |
| `(last user to leave)` | Reporter | Username of the last user who left |

## Your client

| Block | Type | Returns |
| --- | --- | --- |
| `(client username)` | Reporter | Your username |
| `(my client object)` | Reporter | JSON object with your `username`, `user_id`, `system` and `version` |
| `(client IP)` | Reporter | Always `Unavailable in the SDK build` in the current version |
