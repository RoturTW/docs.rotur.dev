---
description: A checklist of what the Developer Terms ask of your app, with where each is covered in these docs.
---

# Meet the Developer Terms

You accept the [Developer Terms](https://rotur.dev/developer-terms) when you create your first app. This page is a practical checklist, not a replacement: if this page and the terms ever disagree, the terms win.

The short version: Rotur looks after accounts, age checks, parental controls, privacy requests and account-level safety. You build your app and moderate your own content.

## Before you launch

- [ ] **Publish a privacy policy** that says what your app collects and why, and show it to people before they sign in. You're responsible for the data your app keeps, including what you get from Rotur.
- [ ] **Let people report** content and other users. Your own form with the [reports API](handle-reports.md#file-a-report-from-your-app), or a link to [Rotur's report page](handle-reports.md#use-the-hosted-report-page), are both fine.
- [ ] **Set a webhook** if you store anything about the people who use your app. See [Receive webhooks](webhooks.md).
- [ ] **Declare truthfully** whether people can talk, whether there's mature content and whether people can spend. See [Declarations and safety signals](safety.md).
- [ ] **Ask signals** before messages and purchases if you declared them, and don't offer a way round a "no".
- [ ] **Keep secrets on your server.** No client secret or webhook signing secret in web pages, downloadable apps or public repositories. If your app can't keep a secret, make it a [public client](../advanced-oauth/client-types.md).
- [ ] **Sign people in with Sign in with Rotur**, not by asking for a Rotur token. Rotur's age, ban and parental checks only cover Sign in with Rotur.

## While your app is running

- [ ] **Look at reports promptly.** Take down content that breaks your rules, Rotur's Terms or the law, and use [bans](bans.md) where they help.
- [ ] **Close every report** as resolved or dismissed, so the person who made it hears the outcome.
- [ ] **Send illegal content to Rotur straight away.** Reports about child sexual abuse, threats to life, encouraging suicide or self-harm, or terrorism go automatically when filed with the right category. [Escalate](handle-reports.md#send-a-report-to-rotur) anything else you think may be illegal. Never copy, download or forward child sexual abuse material, not even to Rotur.
- [ ] **Delete data within 30 days** of a `user.deleted`, `user.left` or `user.banned` event, unless the law makes you keep it.
- [ ] **Answer privacy requests within a month**, if you turned them on.
- [ ] **Update your declarations before** you add chat, mature content or payments.

## Things you must never do

- Ask people for their date of birth or age, or try to work it out from what they do or how they write. Rely on Rotur's age checks.
- Contact an under-18 outside your app or Rotur, or encourage them to move a conversation elsewhere. This applies to everyone on your team.
- Sell what you get from Rotur, use it for advertising, or combine it with other data to profile people.

## If something goes wrong

Rotur can suspend an app, remove it, revoke its secrets or remove someone from its team. A suspended app can't be signed in to and its tokens stop working. Rotur tells the owner why, unless that would put someone at risk or harm an investigation. To ask for a decision to be looked at again, email mist@rotur.dev.

Questions about the terms: mist@rotur.dev. Security problems: security@rotur.dev.
