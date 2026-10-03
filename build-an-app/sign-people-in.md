---
description: Sign people in to your web page with their Rotur account, using the JavaScript SDK.
---

# Sign people in

The JavaScript SDK signs people in on your page, keeps them signed in, and asks for their permission when your app needs it. You need an app ID first: see [Create your app](README.md#create-your-app).

## Install

```bash
npm install rotur-sdk
```

Or, without a build step, load it with a script tag. This puts `Rotur` on `window`:

```html
<script src="https://unpkg.com/rotur-sdk"></script>
```

## Sign in and out

```js
import { Rotur } from "rotur-sdk";

const rotur = new Rotur({ app: "app_0123456789abcdef" });

rotur.onChange((user, denied) => {
  // Runs now, and whenever someone signs in or out, in this tab or another.
  if (user) showUser(user.username, user.avatar);
  else showSignInButton();
  // Set when Rotur signed them out because they can't use your app.
  if (denied) showMessage(denied.message);
});

signInButton.onclick = () => rotur.signIn();
signOutButton.onclick = () => rotur.signOut();
```

`signIn()` opens Rotur's sign-in window and resolves to the person once they've signed in. Call it from a click, or the browser may block the window. If it's blocked anyway, the page goes to Rotur and comes back signed in, and the SDK finishes the sign-in when the page loads.

People stay signed in on that browser until they sign out, or until Rotur stops them using your app: see [When Rotur refuses someone](call-your-server.md#when-rotur-refuses-someone). `rotur.user` is who is signed in now, or `null`:

| Field | What it is |
| --- | --- |
| `id` | Their Rotur ID. It never changes, so key your data by it |
| `username` | Their username. It can change |
| `name` | Their display name, or their username if they haven't set one |
| `avatar` | The address of their avatar |

## Use the Rotur API as them

Once someone is signed in, the SDK's other methods act as them:

```js
await rotur.posts.create("Hello from my app");
const { friends } = await rotur.friends.list();
```

You don't list what your app needs. When a method needs something the person hasn't allowed yet, such as posting for them, the SDK asks them once in a small window and then makes the call. Rotur also remembers what your app uses whenever you or someone else on your app's team allows it, and asks everyone who signs in after that for all of it at once. So try each feature of your app once while signed in as yourself, and other people are only asked once.

If the browser blocks that window, or the person says no, the method throws a `RoturPermissionError`. The SDK doesn't ask again during that page visit. To ask again, call `request()` from a click:

```js
import { RoturPermissionError } from "rotur-sdk";

try {
  await rotur.posts.create(text);
} catch (error) {
  if (!(error instanceof RoturPermissionError)) throw error;
  showButton("Allow posting", async () => {
    await error.request();
    await rotur.posts.create(text);
  });
}
```

The [Rotur SDK](../rotur-sdk/README.md) section documents each method.

## When sign-in doesn't finish

`signIn()` throws an `AuthError`. Its `code` says why:

| `code` | Why |
| --- | --- |
| `closed` | The person closed Rotur's window |
| `access_denied` | The person said no, or Rotur told them they can't use your app and they went back |
| Anything else | Something went wrong at Rotur. `message` says what |

Rotur tells people why they can't use your app on its own screen: for example, that you banned them, that a parent hasn't allowed your app, or that your app is for adults only. Your page isn't told why. Your server is, when it [checks their validator](check-whos-calling.md).

## Next

* [Call your server](call-your-server.md) with `rotur.fetch`.
* [Take payments](payments.md) with `rotur.pay`.
