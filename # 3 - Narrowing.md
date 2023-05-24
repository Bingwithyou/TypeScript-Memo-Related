# Narrowing

The process of refining types to more specific types than declared is called _narrowing_.

```ts
// If without typeof to check type of a value, TypeScript will warning us
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```

## `typeof` type guards

In TypeScript, checking against the value returned by `typeof` is a type guard.

TypeScript expects this to return a certain set of strings:

- "string"
- "number"
- "bigint"
- "boolean"
- "symbol"
- "undefined"
- "object"
- "function"

Notice that in the list above, typeof doesn’t return the string null. Check out the following example:

```ts
function printAll(strs: string | string[] | null) {
  if (typeof strs === "object") {
    for (const s of strs) {
      // error: 'strs' is possibly 'null'.
      console.log(s);
    }
  } else if (typeof strs === "string") {
    console.log(strs);
  } else {
    // do nothing
  }
}
```

> `arrays` and `null` are _object_ types in JavaScript
