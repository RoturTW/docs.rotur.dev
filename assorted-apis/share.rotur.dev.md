# share.rotur.dev

Rotur Shares publish files from your originOS drive to the web. Use them to host a static site, share an image or offer a small download.

> **Base URL:** `https://share.rotur.dev`
>
> **Auth:** None to view files. Publishing needs a Pro subscription (formerly called originDrive) or higher.

## How it works

Any file you put in the `~/share` folder of your originOS user folder is available straight away at:

```
https://share.rotur.dev/<username>/<filename>
```

For example, `~/share/test.html` on the account `mist` is served at <https://share.rotur.dev/mist/test.html>.

<img width="1055" height="646" alt="The share folder in originOS Files" src="https://github.com/user-attachments/assets/94d1af35-7353-406a-8bb4-8f0e85f4d8a1" />

Files are served as static files, so any file type works, including HTML pages. Shared files count toward your file storage limit.

## Set up

1. Subscribe to Pro or higher. See [Subscriptions](../my-account/subscriptions.md).
2. In originOS, open Settings and check that your account shows the tier.
3. Open Files. If there is no `share` folder in your user folder, create one.
4. Drag files into `~/share`.

If your account does not show the tier after you subscribe, message @mistium on Discord to have Rotur Shares and your file size limit enabled.

## Access and limits

| | |
| --- | --- |
| **Who can see shared files** | Anyone with the URL. Everything in `~/share` is public. |
| **Search engines** | Files are not indexed unless another indexed page links to them. |
| **Bandwidth** | No fixed limit. Accounts that send excessive requests or serve large files with heavy bandwidth use may be throttled. |
| **Tiers** | Pro (formerly originDrive) or higher. Not available on free accounts. |

{% hint style="warning" %}
Do not put anything private in `~/share`. Every file there is publicly reachable by anyone who knows or guesses its URL.
{% endhint %}
