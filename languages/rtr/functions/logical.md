# Logical functions

These functions treat `false`, `0`, `""`, and `null` as false, and every other value as true.

| Function | Returns |
| --- | --- |
| `all(a, b, ...)` | `true` if every argument is true |
| `any(a, b, ...)` | `true` if at least one argument is true |
| `not(x)` | `true` if `x` is false, otherwise `false`. `!(x)` does the same. |

## all

```js
all(true, true, true)   /* true */
all(true, false, true)  /* false */
all(true, 1, "hello")   /* true */
```

## any

```js
any(false, false, true)   /* true */
any(false, false, false)  /* false */
any(false, 0, "hello")    /* true */
```

## not

```js
not(true)   /* false */
not(false)  /* true */
not(0)      /* true */
not(1)      /* false */
```
