# Linking

Rotur linking is done through [https://rotur.dev/link](https://rotur.dev/link), it allows external programs to get access to your rotur token simply without needing to go through any login process on device, instead offloading that work to other devices.

The basic auth flow is as follows:

GET [https://api.rotur.dev/link/code](https://api.rotur.dev/link/code)

```javascript
// STATUS 200
{
  "code": "AUTH CODE" // this will always be 6 letters long
}
```

Then simply display the code to the user and direct them to open https://rotur.dev/link on another device, they can then go through the entire auth flow without you needing to do any auth yourself.

Once the user has completed authenticating, you must have a button or an action that allows them to click to signal "i have finished authenticating". Once this button is clicked, simply request to

GET [https://api.rotur.dev/link/user?code=](https://api.rotur.dev/link/user?code=)

<pre class="language-javascript"><code class="lang-javascript"><strong>// STATUS 404
</strong><strong>{
</strong>  "linked": false,
  "token":  ""
}

// STATUS 200
{
  "linked": true,
  "token": "rotur auth token"
}
</code></pre>

From here you can use the rotur auth token with any other rotur services or get user data with:  [get-user-data.md](../your-connection/authentication/get-user-data.md "mention")
