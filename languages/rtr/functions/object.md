# Object functions

| Function | Returns |
| --- | --- |
| `obj()` | A new empty object |
| `keys(object)` | An array of the object's property names |
| `values(object)` | An array of the object's property values |
| `has(object, key)` | `true` if the object has the property `key` |
| `set(object, key, value)` | Sets `key` to `value` and returns the object |
| `del(object, key)` | Removes `key` and returns the object |

## obj

```js
person = obj()
person.name = "John"
person.age = 30
```

## keys

```js
keys(person)  /* ["name", "age"] */
keys(obj())   /* [] */
```

## values

```js
values(person)  /* ["John", 30] */
values(obj())   /* [] */
```

## has

```js
has(person, "name")     /* true */
has(person, "address")  /* false */
```

## set

Use `set` when the property name is in a variable.

```js
field = "city"
set(person, field, "Oslo")
log(person.city)  /* Oslo */
```

## del

```js
del(person, "age")
has(person, "age")  /* false */
```
