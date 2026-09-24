# RDF

RDF (Rotur Data Format) is a data storage format that can restrict each property to a type and to conditions, checked at runtime. It runs in JavaScript environments.

{% @github-files/github-code-block url="https://github.com/RoturTW/.rdf" %}

```js
{
    string name = "Swallow";
    object job = {
        title = "Sr. Nest Maker";
        company = "Nests R Us";
        number yearsOfExperience = 2;
    };
    number age = 4 where value > 1;
    array<number> scores = [] where each > 0;
}
```

Because types and constraints are checked whenever a value is set, a bug cannot replace a value with one of the wrong type, or one that breaks a rule, without an error. Rules such as length checks or number ranges live with the data instead of in your program.

## Install

Load the highest-numbered file in the repository's `src` folder (currently `v03.js`) into your project. It defines `RDF` on `window`.

## Syntax

A document is a set of properties inside curly braces. End each property with `;`.

Start a comment with `#`. A comment runs until the next `;`, so end it with `;` unless it is the last line.

```js
[type] name = value [where condition];
```

### Values

| Value | Example |
| --- | --- |
| String | `"James"` (double quotes only) |
| Number | `10`, `-2.5` |
| Boolean | `true`, `false` |
| Array | `[1, 2, 3]`, `[]` |
| Object | `{ name = "James"; }` |

### Types

Put a type before the name to restrict what the property can hold. A property without a type accepts any value.

| Type | Accepts |
| --- | --- |
| `string` | Strings |
| `number` | Numbers |
| `boolean` | `true` or `false` |
| `object` | Objects (not arrays or `null`) |
| `array` | Arrays |
| `array<type>` | Arrays whose elements are all of `type`, for example `array<string>` |
| `any` | Any value |

```js
{
    number age = 10;
    array<string> tags = ["a", "b"];
}
```

### Constraints

Add `where` and a condition to restrict the value. `value` stands for the property's value. For arrays, use `where each` to check every element.

```js
{
    number age = 10 where value > 0;
    string name = "James" where length(value) > 4;
    array<number> scores = [3, 5] where each > 0;
}
```

Conditions are expressions in a reduced version of [RTR](rtr/README.md). They support the operators `==`, `!=`, `>`, `<`, `>=`, `<=`, `+`, `-`, `*`, `/`, `%`, `^`, and functions such as `length`, `min`, `max`, `abs`, `round`, `floor`, `ceil`, `sqrt`, `join`, `split`, `has`, `all`, `any`, `not`, `toNum`, and `toStr`.

Each property takes one constraint.

{% hint style="warning" %}
Constraints on properties inside a nested object are not supported: the parser reads the first `where` as belonging to the outer property. Put constrained properties at the top level.
{% endhint %}

## Errors

Parsing throws an error if a value does not match its type or constraint. Parse errors start with `RDF <version> error:`. After parsing, assigning an invalid value to a property, or pushing an invalid element into an `array<type>` property with `push`, `unshift`, or `splice`, also throws, with messages such as `Constraint violation for 'age'` or `Constraint violation for element of 'scores'`.

| Error | Cause |
| --- | --- |
| `Data must be enclosed in curly braces` | The document does not start with `{` and end with `}` |
| `Invalid token: …` | A property is not in the form `name = value` |
| `Type mismatch: '<name>' must be a <type>` | The value is the wrong type |
| `Constraint violation for '<name>'` | The value fails its `where` condition |
| `Type mismatch in array '<name>'` | An element of an `array<type>` is the wrong type |
| `Type or constraint mismatch in array '<name>'` | An element of an `array<type>` with `where each` fails the type or the condition |
| `Constraint violation in array '<name>'` | An element of an untyped array fails its `where each` condition |

## Methods

### RDF.parse()

Parses RDF text into an object. Read and assign its properties like any JavaScript object; assignments are type-checked and constraint-checked.

```javascript
const person = RDF.parse(`{ number age = 10 where value > 0; }`);
person.age = 11;  // ok
person.age = -1;  // throws: Constraint violation for 'age'
```

### RDF.stringify()

Converts an object back into RDF text, including each property's type and constraint. The second argument is the number of spaces to indent by. With `0` or no indent, the output is on one line.

```javascript
// RDF.stringify(object, indentation)

console.log(RDF.stringify({"hello": "world"}, 2))
/*
{
  hello = "world";
}
*/
```

### RDF.setProperty()

Adds a typed property to any object, including plain JavaScript objects, using the same syntax as an RDF document. It returns the object.

```javascript
// RDF.setProperty(object, property_string)

const person = RDF.parse(`{ number age = 10 where value > 0; }`);
RDF.setProperty(person, `string name = "James" where length(value) > 4`);
```
