# String functions

| Function | Returns |
| --- | --- |
| `join(a, b, ...)` | All arguments joined into one string, with no separator |
| `split(string, separator)` | An array of the parts of `string` between each `separator` |
| `chr(code)` | The character with character code `code` |
| `ord(string)` | The character code of the first character of `string` |
| `length(string)` | The number of characters in `string` |
| `toStr(value)` | `value` converted to a string |

## join

```js
join("Hello ", "World")  /* "Hello World" */
join("a", "b", "c")      /* "abc" */
```

## split

```js
split("Hello World", " ")  /* ["Hello", "World"] */
split("a-b-c", "-")        /* ["a", "b", "c"] */
```

## chr

```js
chr(65)  /* "A" */
chr(97)  /* "a" */
```

## ord

```js
ord("A")  /* 65 */
ord("a")  /* 97 */
```

## length

```js
length("Hello")  /* 5 */
length("")       /* 0 */
```

## toStr

```js
toStr(5)  /* "5" */
```
