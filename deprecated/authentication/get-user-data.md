# Get User Data

{% hint style="warning" %}
This page documents the legacy Rotur websocket, which is deprecated but kept around as a reference. For new projects, we recommend the REST API at [https://api.rotur.dev](https://api.rotur.dev) together with the [Rotur SDK](../../rotur-sdk/README.md).
{% endhint %}


To fetch a user's data from Rotur, hit the `/get_user` endpoint:

> **Note:** The `password` needs to be an **MD5 hash**, not plain text.

***

## Example (JavaScript)

```js
const username = "your_username";
const password = "md5_hashed_password"; // hash it before sending

// you can also pass an Authorization header token instead of password login
// if you don't have the username and password

fetch(`https://api.rotur.dev/get_user?username=${username}&password=${password}`)
  .then(res => res.json())
  .then(data => {
    if (data.error) {
      // e.g. "invalid credentials"
      throw new Error(data.error);
    }

    console.log("User data:", data); // this is the rotur account object
  })
  .catch(err => {
    console.error("Failed to fetch user data:", err);
  });
```

***

The response (`data`) is a full **Rotur account object**. Details on what's inside: [rotur-account-objects](../../my-account/rotur-account-objects/)

***

## Logging in via WebSocket

Once you have the user data, you can auth with the WebSocket server using the `auth` command. This is the recommended way to log in. It is much faster than doing it through HTTP.

Docs for that here: [login-to-rotur-auth.md](../websocket-commands/login-to-rotur-auth.md)
