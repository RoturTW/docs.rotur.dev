---
description: Let people report content and users in your app, handle the reports, and send serious ones to Rotur.
---

# Handle reports

Every app must let people report content and other users ([Developer Terms](developer-terms.md), section 3). Reports go to your app's own queue: you moderate your app's content. Serious reports also go to Rotur.

You can take reports in two ways, and use both:

* **Link to Rotur's report page.** Rotur hosts the form and files the report for you. The least work.
* **File reports from your app** with the API, from your own report button or form.

## Use the hosted report page

Link people to:

```
https://rotur.dev/apps/<slug>/report?user=<username>&content=<content id>&text=<what they saw>
```

| Parameter | Required | What it is |
| --- | --- | --- |
| `user` | Yes | The username of the person being reported (a leading `@` is fine). Up to 40 characters |
| `content` | No | Your ID for the message, post or other content. Leave it out to report the person. Up to 200 characters |
| `text` | No | The content as they saw it, kept as a snapshot. Up to 1000 characters |

Encode each value, for example with `URL.searchParams`:

```js
const url = new URL("https://rotur.dev/apps/sketchpad/report");
url.searchParams.set("user", message.author);
url.searchParams.set("content", message.id);
url.searchParams.set("text", message.text);
window.open(url, "_blank");
```

The person signs in to Rotur if they need to, picks a category, adds details and sends it. Your team doesn't learn who reported something through this page; only Rotur staff can see that.

## Categories

| Category | Use for |
| --- | --- |
| `spam` | Spam |
| `harassment` | Bullying or harassment |
| `hate` | Hate against people for who they are |
| `sexual` | Sexual content |
| `violence` | Violence or gore |
| `scam` | Scams and fraud |
| `other` | Anything else |
| `csea` | **Priority.** Child sexual exploitation or abuse |
| `threat_to_life` | **Priority.** A threat to someone's life |
| `self_harm` | **Priority.** Encouraging suicide or self-harm |
| `terrorism` | **Priority.** Terrorism |

Reports in a priority category go to Rotur's safety team as soon as they're filed, and you can't close them yourself.

{% hint style="danger" %}
If you come across child sexual abuse material, don't copy, download or forward it, not even to Rotur. File a report with the category `csea` and the content's ID, and hide the content in your app.
{% endhint %}

## File a report from your app

`POST /v2/apps/<app>/reports`

**Auth:** [App credentials](apps-api.md). A person's own Rotur account token with the `reports:create` permission also works, and then they are the reporter. Access tokens from Sign in with Rotur can't file reports.

### Parameters

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `target_type` | body | string | Yes | `content` or `user` |
| `target_id` | body | string | For `content` | Your ID for the content. Up to 200 characters. Ignored for `user` |
| `target_user` | body | string | Yes | The username or Rotur user ID of the person being reported |
| `category` | body | string | Yes | One of the [categories](#categories) |
| `reason` | body | string | No | What happened, in the reporter's words. Up to 1000 characters |
| `snapshot` | body | string | No | The content as it was, so it survives being edited or deleted. Cut to 4000 characters |
| `reporter` | body | string | No | App credentials only. The username or ID of the user who reported it. Must be one of [your users](users.md) |

If you name a `reporter`, your team sees who it was in the report, because your app already knew. Leave it out to file the report as the app.

### Example

```sh
curl -X POST https://api.rotur.dev/v2/apps/$APP/reports \
  -u "$APP:$ROTUR_CLIENT_SECRET" -H "Content-Type: application/json" \
  -d '{"target_type": "content", "target_id": "msg_123", "target_user": "grimtag",
       "category": "harassment", "reason": "Keeps insulting me",
       "snapshot": "the message text", "reporter": "kit"}'
```

**Response `201`:**

```json
{ "report_id": "Qm9vX2FyZV9yZXBvcnQ", "status": "open", "escalated": false }
```

A report in a priority category comes back with `"status": "escalated"` and `"escalated": true`.

### Errors

| Status | When |
| --- | --- |
| `400` | A field is missing or invalid. `error` says which, for example `target_type must be content or user` |
| `400` | `reporter` isn't one of your users, or the reporter and the reported person are the same |
| `404` | `target_user` doesn't exist |
| `409` | The same reporter already has an open report about the same thing (`you have already reported this`) |

## List reports

`GET /v2/apps/<app>/reports`

Lists your app's reports, newest first.

**Auth:** [App credentials](apps-api.md), or the dashboard.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `status` | query | string | No | `open`, `resolved`, `dismissed`, `escalated`, or `all` (the default) |

**Response `200`:**

```json
{
  "reports": [
    {
      "id": "Qm9vX2FyZV9yZXBvcnQ",
      "target_type": "content",
      "target_id": "msg_123",
      "target_user": { "id": "1f2e3d4c-…", "username": "grimtag", "avatar": "https://avatars.rotur.dev/1f2e3d4c-…" },
      "category": "harassment",
      "priority": false,
      "reason": "Keeps insulting me",
      "snapshot": "the message text",
      "status": "open",
      "created_at": 1790000000000,
      "note": "",
      "reporter": { "id": "7ebdf483-…", "username": "kit", "avatar": "https://avatars.rotur.dev/7ebdf483-…" },
      "from_rotur_user": false,
      "escalated": false,
      "auto_escalated": false,
      "escalated_at": null,
      "outcome": "",
      "resolved_by": null,
      "resolved_at": null
    }
  ],
  "categories": ["spam", "harassment", "hate", "sexual", "violence", "scam", "other", "csea", "threat_to_life", "self_harm", "terrorism"]
}
```

* `reporter` is set only when your app named the reporter. When a Rotur user reported through Rotur, `reporter` is `null` and `from_rotur_user` is `true`.
* `resolved_by` is the team member who closed the report from the dashboard, or `null` if your app closed it.
* `outcome` is Rotur's decision on an escalated report once it's made: `action_taken` or `no_action`.
* Times are Unix milliseconds. Closed reports are deleted two years after they were closed.

## Close a report

`POST /v2/apps/<app>/reports/<id>/resolve`

**Auth:** [App credentials](apps-api.md), or the dashboard.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `outcome` | body | string | Yes | `resolved` if you took action, or `dismissed` if nothing broke your rules |
| `note` | body | string | No | A private note for your team. Up to 1000 characters |

```sh
curl -X POST https://api.rotur.dev/v2/apps/$APP/reports/$REPORT/resolve \
  -u "$APP:$ROTUR_CLIENT_SECRET" -H "Content-Type: application/json" \
  -d '{"outcome": "resolved", "note": "Removed the message and banned for a week"}'
```

**Response `200`:** the report.

If a Rotur user made the report, Rotur tells them whether you took action. It never says what you did, or who on your team decided.

| Status | When |
| --- | --- |
| `400` | `outcome` isn't `resolved` or `dismissed`, or `note` is too long |
| `404` | No such report |
| `409` | The report is with Rotur (`Rotur is reviewing this report, so it can't be closed here`) |

## Send a report to Rotur

`POST /v2/apps/<app>/reports/<id>/escalate`

Sends an open report to Rotur's moderation queue. Do this for anything that may be illegal, or that breaks Rotur's rules beyond your app. Rotur staff see what you saw, including the snapshot, and can act on the account across Rotur.

**Auth:** [App credentials](apps-api.md), or the dashboard.

| Name | In | Type | Required | Description |
| --- | --- | --- | --- | --- |
| `note` | body | string | No | Context for Rotur's staff. Up to 1000 characters |

**Response `200`:** the report, now `escalated`.

| Status | When |
| --- | --- |
| `404` | No such report |
| `409` | It's already with Rotur, or it isn't open |
