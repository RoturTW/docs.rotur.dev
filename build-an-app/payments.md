---
description: Ask people to pay in Rotur credits. They approve each payment in Rotur's own window, and your server hears when it's paid.
---

# Take payments

People pay in Rotur credits, and approve each payment in Rotur's own window with their password. Your app never needs permission to spend their credits.

## From the page

`rotur.pay` opens Rotur's payment window and resolves once the person has answered. Call it from a click:

```js
const { status } = await rotur.pay({ to: "kit", amount: 5, note: "Sticker pack" });
// "paid", "declined", "cancelled", "expired", or "closed" if they closed the window
```

`to` is who gets paid, by username or Rotur ID. `note` (up to 160 characters) tells the payer what it's for. `amount` is in credits, from 0.01 to 1,000,000.

This is enough for tips and donations. Don't trust it to unlock anything: the page can be changed to say `paid` when nothing was.

## When your server delivers something

Let your server make the request and hear when it's paid:

1. The page asks your server for an order: `rotur.fetch("/api/orders", { method: "POST" })`.
2. Your server works out who is calling, then makes a payment request with its secret, naming them as the payer and your order ID as the `reference`:

   ```
   POST /v2/apps/<app ID>/payment-requests
   { "payer": "<their Rotur ID>", "to": "kit", "amount": 5, "note": "Sticker pack", "reference": "order-42" }
   ```

   [Use your app secret on the server](app-secret.md) has this call in each language. Send the answer back to the page.
3. The page passes it to `rotur.pay`, which opens it for the person to approve:

   ```js
   const request = await rotur.fetch("/api/orders", { method: "POST" }).then((res) => res.json());
   const { status } = await rotur.pay(request);
   ```

4. When they pay, Rotur sends your server a `payment.completed` [webhook](webhooks.md) with your `reference`. Deliver the order then. Without a webhook, ask Rotur instead: `GET /v2/apps/<app ID>/payment-requests/<id>` answers with its `status`.

A payment request lasts 15 minutes. Your server can cancel one that's still pending with `POST /v2/apps/<app ID>/payment-requests/<id>/cancel`.

## A pay link

To let anyone pay someone, without your app making a request, link to their pay page:

```js
import { payLink } from "rotur-sdk";

payLink("kit", { amount: 5, note: "Thanks for the stream" });
// https://rotur.dev/pay/kit?amount=5&note=Thanks+for+the+stream
```

## Spending limits

A parent can limit what a teen spends. Rotur checks this when the person approves, and tells them if it stops the payment. If your app sells things for credits without a payment request, ask first with the [purchase signal](safety.md#may-they-spend).
