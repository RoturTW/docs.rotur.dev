# Gifts

`rotur.gifts` creates gift codes that hold credits. Anyone with the code can claim the credits, so you can send credits to someone who does not have an account yet. `get()` needs no token; every other method does.

## rotur.gifts.create(amount, options?)

Creates a gift code paid for from your balance. The response shows the tax and the total you paid.

**Auth:** Required. Sub-tokens need `gifts:create`.

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `amount` | number | Yes | Credits in the gift |
| `options.note` | string | No | Note shown with the gift |
| `options.expiresInHrs` | number | No | Hours until the code expires |

```ts
const gift = await rotur.gifts.create(100, {
  note: "Happy birthday",
  expiresInHrs: 48,
});
console.log(gift.claim_url);
```

**Returns:** `{ message, id, code, amount, tax, total_paid, expires_at, claim_url }`

## rotur.gifts.get(code)

Gets a gift's public details.

**Auth:** None.

```ts
const { gift } = await rotur.gifts.get("CODE");
```

**Returns:** `{ gift: { code, amount, note, creator_id, expires_at } }`

## rotur.gifts.claim(code)

Claims a gift and adds its credits to your balance.

**Auth:** Required. Sub-tokens need `gifts:claim`.

```ts
const { amount, new_balance } = await rotur.gifts.claim("CODE");
```

**Returns:** `{ message, amount, new_balance }`

## rotur.gifts.cancel(id)

Cancels a gift you created and refunds it. Takes the gift `id`, not the code.

**Auth:** Required. Sub-tokens need `gifts:cancel`.

```ts
const { refunded } = await rotur.gifts.cancel("gift-id");
```

**Returns:** `{ message, refunded, new_balance }`

## rotur.gifts.mine()

Lists the gifts you created.

**Auth:** Required. Sub-tokens need `gifts:view`.

```ts
const { gifts, count } = await rotur.gifts.mine();
```

**Returns:** `{ gifts, count }`. Each gift is `{ id, code, amount, note, creator_id, created_at, expires_at, claimed_at?, claimed_by? }`.
