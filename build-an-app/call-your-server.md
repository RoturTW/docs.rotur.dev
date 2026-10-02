---
description: Call your own server from the browser with rotur.fetch, so your server knows who is calling.
---

# Call your server

`rotur.fetch` works like `fetch`, and adds a header that proves who is signed in:

```js
const res = await rotur.fetch("/api/scores", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ score: 42 }),
});
```

The header is `Authorization: Rotur <validator>`. A validator is a short proof from Rotur that the signed-in person made it, for your app and nothing else. The SDK gets a new one every 5 minutes and reuses it until then. Your server [checks it with Rotur](check-whos-calling.md).

When nobody is signed in, `rotur.fetch` sends the request without the header.

## When your server says no

Your server answers `401` when it can't tell who is calling. The examples in [Check who's calling](check-whos-calling.md) send Rotur's `error` and `code` with it:

```js
if (res.status === 401) {
  const { error } = await res.json();
  showMessage(error ?? "Sign in to continue");
}
```

`error` is written for the person, for example "You've been banned from Sketchpad."

Once someone can't use your app, Rotur also stops renewing their sign-in. `rotur.fetch` then throws an `ApiError` and signs them out on that browser, and `rotur.onChange` tells your page. If they sign in again, Rotur's window tells them why they can't.

## A server on another origin

If your server isn't on the same origin as your page, for example `https://api.example.com` serving `https://example.com`, it must allow the `Authorization` header with CORS:

```http
Access-Control-Allow-Origin: https://example.com
Access-Control-Allow-Headers: Authorization, Content-Type
Access-Control-Allow-Credentials: true
```

Pass `credentials: "include"` to `rotur.fetch` if your server also keeps a session cookie.

## Page loads, forms and WebSockets

Only requests made with `rotur.fetch` carry a validator. The examples in [Check who's calling](check-whos-calling.md) start a session cookie on the first one, so later page loads, form posts and WebSocket connections to the same site know who is calling. Call your server with `rotur.fetch` once after signing in to start it.

## Next

* [Check who's calling](check-whos-calling.md), on your server.
