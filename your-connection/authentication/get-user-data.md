# Get User Data

To fetch a user's data from Rotur, hit the `/get_user` endpoint:

> **Note:** The `password` needs to be an **MD5 hash**, not plain text.

***

## Example (JavaScript)

```js
const username = "your_username";
const password = "md5_hashed_password"; // hash it before sending

// you can also use ?auth=token with a rotur token
// if you dont have the username and password

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

The response (`data`) is a full **Rotur account object**. Details on what’s inside: [rotur-account-objects](../../my-account/rotur-account-objects/)

***

## Logging in via WebSocket

Once you have the user data, you can auth with the WebSocket server using the `auth` command. This is the recommended way to log in — way faster than doing it through HTTP.

Docs for that here: [login-to-rotur-auth.md](../websocket-commands/login-to-rotur-auth.md)
