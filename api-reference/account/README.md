---
description: What a Rotur account holds, and the pages that explain each part.
---

# Your account

Every Rotur account is a set of keys: some the person sets (such as `bio` and `pronouns`), and some Rotur manages (the `sys.` keys). These pages explain them.

| Page | What it covers |
| --- | --- |
| [Account objects](account-objects/README.md) | Every key on an account, who can change it, and what apps can read |
| [originOS keys](account-objects/originos-specific-keys.md) | Keys originOS stores on accounts |
| [Badges](badges.md) | The badges Rotur gives, and how people hide and order them |
| [Bio template strings](bio-templates.md) | Live values people can put in their bio |
| [Friend notes](friend-notes.md) | Private notes about friends |
| [Subscriptions](subscriptions.md) | Plans and what each includes |
| [Request OFSF storage](request-ofsf-storage.md) | Asking for more file storage |

To read or change an account, use the [Claw API](../../claw/api-endpoints/README.md): [`GET /me`](../../claw/api-endpoints/me.md) for your own account and [`GET /profile`](../../claw/api-endpoints/profile.md) for anyone's public profile.

What an app can read depends on its token. A Sign in with Rotur token sees the public profile. A Rotur account token sees what its [permissions](../../accounts-and-tokens/tokens/permissions.md) cover.
