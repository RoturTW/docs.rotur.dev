# Overview

The Rotur extension adds Rotur accounts to TurboWarp projects: login, account keys, cloud storage, messaging between users, friends, credits, keys and badges. These pages document version 9 of the extension, which shows up in the block palette as **RoturV9**.

## Add the extension

The extension is loaded from:

```
https://extensions.mistium.com/featured/Rotur.js
```

1. In the TurboWarp editor, click **Add Extension** in the bottom-left corner.
2. Choose **Custom Extension** and paste the URL above. You can also paste the extension's code on the **Text** tab.
3. Turn on **Run extension without sandbox**, then click **Load**.

{% hint style="warning" %}
The extension has to run unsandboxed. If it's loaded in the sandbox, it throws "Rotur must run unsandboxed." and doesn't load.
{% endhint %}

## Block notation

These pages write blocks as text, using the same shapes as the editor:

| Written as | Block type |
| --- | --- |
| `connect to server with designation: [rtr] ...` | Command |
| `(get balance)` | Reporter (returns a value) |
| `<authenticated>` | Boolean |
| `when connected to server` | Hat (event) |

`[...]` is an input, and the text inside it is the default value.

Most blocks that need an account return `Not Connected` when the socket isn't open, and `Not Logged In` when there's no logged-in account. Most booleans return `false` in both cases instead. Lists and objects come back as JSON text.

## Pages in this section

| Page | What it covers |
| --- | --- |
| [Connect to Rotur](connecting-to-rotur.md) | Connecting, logging in with the login prompt or a token, and logging out |
| [Account keys](account-keys.md) | Reading and setting keys on the logged-in account |
| [Data storage](data-storage.md) | Per-project cloud storage on the user's account |
| [Packets](packets.md) | Sending messages to other users on ports, the packet queues, and synced variables |
| [Designations](rotur-designations.md) | Your designation, and checking who else is connected on it |
| [Rmail](rmail.md) | Sending and reading mail between connected users |
| [Friends](friends.md) | Friend lists, friend requests and their events |
| [Currency](currency.md) | Balance, transfers and transaction history |
| [Items](items.md) | Buying, checking and reading keys |
| [Badges](badges.md) | The badges on the logged-in account |
