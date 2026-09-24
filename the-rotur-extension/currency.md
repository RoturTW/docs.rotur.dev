# Currency

Rotur accounts hold credits, which users can send to each other and spend on keys. These blocks read the logged-in user's balance and history and send credits. Users get credits from the daily claim on Rotur; the extension has no block for claiming. See [Transactions and taxes](../my-account/transactions-and-taxes.md) for fees.

## Get balance

```
(get balance)
```

Reporter. The logged-in user's credit balance. It's loaded at login and refreshed every 15 seconds, and after each transfer or purchase you make with the extension.

## Transfer credits

```
(transfer [0] to [user])
```

Reporter. Sends `AMOUNT` credits to `USER`. Amounts are rounded to 2 decimal places.

| Returns | When |
| --- | --- |
| `Success` | The credits were sent |
| `Invalid amount` | `AMOUNT` isn't a number, or is 0 or less |
| `Minimum amount is 0.01` | `AMOUNT` rounds to less than 0.01 |
| `Cannot send credits to yourself` | `USER` is the logged-in user |
| `recipient user not found` | No account has that username |
| `insufficient funds (required: 5.00, available: 2.00)` | You don't have enough credits. The numbers are the amount and your balance. |

## When balance changed

```
when balance changed
```

Hat. Fires when the balance differs from the last check. The balance is checked every 15 seconds, so the hat can fire up to 15 seconds after the change. Transfers and purchases made with the extension update the balance without firing it.

## Get transactions

```
(get transactions)
```

Reporter. JSON array of the account's transactions, read from the `sys.transactions` account key.

Example:

```json
[
  {
    "type": "out",
    "user": "(user ID of the other account)",
    "amount": 5,
    "note": "transfer",
    "time": 1723684583612,
    "new_total": 12
  }
]
```

`type` is `in` for credits received and `out` for credits sent. Other types, such as key sales and gifts, are listed in [Transactions and taxes](../my-account/transactions-and-taxes.md).
