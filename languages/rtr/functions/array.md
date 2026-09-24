# Array functions

| Function | Returns |
| --- | --- |
| `length(array)` | The number of elements |
| `item(array, index)` | The element at `index`. Indexes start at `0`. |
| `range(start, end)` | An array of the integers from `start` to `end`, including both |

## length

```js
numbers = [1, 2, 3, 4, 5]
length(numbers)  /* 5 */
length([])       /* 0 */
```

## item

```js
numbers = [1, 2, 3, 4, 5]
item(numbers, 2)  /* 3 */
item(numbers, 0)  /* 1 */
```

## range

```js
range(1, 5)  /* [1, 2, 3, 4, 5] */
range(0, 2)  /* [0, 1, 2] */
```

Use `range` with `for` to count:

```js
for (i, range(1, 3)) {
    log(i)
}
```
