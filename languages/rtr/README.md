# RTR

RTR is a small, event-driven scripting language for Rotur websites and apps. A program is a set of event blocks, and the program that hosts RTR runs each block when its event happens. [RWL](../rwl/README.md) pages use RTR for scripts, and [RDF](../rdf.md) uses a reduced form of it for constraints.

```js
event (onload) {
    /* Create a person object */
    person = obj()
    person.name = "John"
    person.age = 30

    /* Store a function on it */
    person.greet = (name)~{
        return(join("Hello, ", name, "! I am John"))
    }

    message = person.greet("Alice")
    log(message)  /* Hello, Alice! I am John */
}
```

## Implementations

The source is at [git.rotur.dev/rotur/rtr](https://git.rotur.dev/rotur/rtr). The older [RoturTW/.rtr](https://github.com/RoturTW/.rtr) repository on GitHub is deprecated.

| Implementation | Location | Notes |
| --- | --- | --- |
| RTR micro | `src/micro` (latest `v17.js`) | The JavaScript interpreter these docs describe. Reports `rtr.version` as `1.6`. |
| RTR 2.0 | `src/2.0` | A TypeScript rewrite in progress. It adds operator precedence and `this` in methods, but does not yet support array or object literals, `%`, `^`, or comments. |

## Pages

| Page | Contents |
| --- | --- |
| [Basics](basics.md) | Variables, operators, control flow, functions, and objects |
| [Structure](structure.md) | How a program is laid out, statements, expressions, and scope |
| [Functions](functions/README.md) | Built-in functions |
| [Events](events.md) | How events run |
| [Examples](examples.md) | Short example programs |
