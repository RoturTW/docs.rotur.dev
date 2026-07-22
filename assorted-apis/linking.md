# Linking

Linking lets an external program get a user's rotur token without running its own login flow. Your app shows a short code, the user enters it at [https://rotur.dev/link](https://rotur.dev/link) on any device, and your app collects the token.

> **Base URL:** `https://api.rotur.dev/`
>
> The same endpoints exist under `https://api.rotur.dev/v2/link` with identical paths.

{% hint style="info" %}
Codes expire **10 minutes** after they are created. If a code expires before the user finishes, just request a new one.
{% endhint %}

## The flow

### 1. Get a code

```http
GET /link/code
```

No authentication needed.

```json
// STATUS 200
{
  "code": "A1B2C3"
}
```

The code is always 6 characters (hex digits 0-9 and A-F).

### 2. Show the code to the user

Display the code and direct the user to open [https://rotur.dev/link](https://rotur.dev/link) on another device. They log in there and enter your code. You do not handle any credentials yourself.

Behind the scenes, that page calls:

```http
POST /link/code?code=A1B2C3
```

This endpoint requires authentication (the user's token, with the `account:settings` permission for sub-tokens) and attaches the user's token to the code. It responds with `200` and the JSON string `"Linked Successfully"`, or `404` with `{"error": "No auth code found"}` if the code is unknown or expired. You only need to call this yourself if you are building your own link page.

### 3. Check the status (optional)

```http
GET /link/status?code=A1B2C3
```

```json
// STATUS 200, the user has linked
{ "status": "linked" }

// STATUS 404, not linked yet, expired, or unknown code
{ "status": "not found" }
```

This does not consume the code, so it is safe to poll.

### 4. Collect the token

Once the user signals they are done (for example by clicking an "I have finished authenticating" button in your app), request:

```http
GET /link/user?code=A1B2C3
```

```json
// STATUS 200
{
  "linked": true,
  "token": "rotur auth token"
}

// STATUS 404
{
  "linked": false,
  "token": ""
}
```

{% hint style="warning" %}
A successful call to `/link/user` deletes the code. You get the token exactly once, so store it.
{% endhint %}

From here you can use the rotur auth token with any other rotur services or get user data with: [get-user-data.md](../deprecated/authentication/get-user-data.md "mention")
