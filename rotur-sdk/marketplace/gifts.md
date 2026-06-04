# Gifts

Accessed via `rotur.gifts`. Gift codes let you send credits to someone (even non-users).

## Create a Gift

```ts
const gift = await rotur.gifts.create(100, {
  note: "Happy birthday!",
  expiresInHrs: 48,
});
```

## Get Gift Info

```ts
const { gift } = await rotur.gifts.get("CODE-HERE");
```

## Claim a Gift

```ts
await rotur.gifts.claim("CODE-HERE");
```

## Cancel a Gift

```ts
await rotur.gifts.cancel("gift-id");
```

## List Your Gifts

```ts
const { gifts, count } = await rotur.gifts.mine();
```
