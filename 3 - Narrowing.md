# Narrowing

## Truthiness narrowing

Values like:

- `0`

- `NaN`

- `""` (the empty string)

- `0n` (the bigint version of zero)

- `null`

- `undefined`

all coerce to `false`, and other values get coerced to `true`.

## Equality narrowing

```ts
interface Container {
  value: number | null | undefined;
}

function multiplyValue(container: Container, factor: number) {
  // Remove both 'null' and 'undefined' from the type.
  if (container.value != null) {
    console.log(container.value);

    // Now we can safely multiply 'container.value'.
    container.value *= factor;
  }
}
let test: Container = { value: 6 };
multiplyValue(test, 6); // expected output: 36
```

```js
undefined == null; // true
undefined === null; // false;
```

## The `in` operator narrowing

Human could both swim and fly, use `?` chooseble parameter

```ts
type Fish = { swim: () => void };
type Bird = { fly: () => void };
type Human = { swim?: () => void; fly?: () => void };

function move(animal: Fish | Bird | Human) {
  if ("swim" in animal) {
    animal;
  } else {
    animal;
  }
}

let person1: Human = { swim: () => console.log("I can swim") };

move(person1); // expected output: I can swim
```

## `instanceof` narrowing

In JavaScript `x instanceof Foo` checks whether the _prototype chain_ of `x` contains `Foo.prototype`.

```ts
function logValue(x: Date | string) {
  if (x instanceof Date) {
    console.log(x.toUTCString());
  } else {
    console.log(x.toUpperCase());
  }
}
```

## Assignments

```ts
let x = Math.random() < 0.5 ? 10 : "hello world!";

let x: string | number;
x = 1;
console.log(x);

x = "goodbye!";
console.log(x);

x = true;
// error: Type `boolean` is not assignable to type `string | number`.
```

Even though the observed type of `x` changed to `number` after our first assignment, we were still able to assgin a `string` to `x`.

This is because the _declared type_ of `x` - the type that `x` started with - is `string | number`

## Control flow analysis

TypeScript was able to analyze this code and see that the rest of the body `(return padding + input;)` is unreachable in the case where `padding` is a `number`.

```ts
function padLeft(padding: number | string, input: string) {
  if (typeof padding === "number") {
    return " ".repeat(padding) + input;
  }
  return padding + input;
}
```

This analysis of code based on reachability is called _control flow analysis_, and TypeScript uses this flow analysis to narrow types as it encounters type guards and assignments.

When a variable is analyzed, control flow can split off and re-merge over and over again:

```ts
function example() {
  let x: string | number | boolean;

  x = Math.random() < 0.5;

  console.log(x);
  // x: boolean;

  if (Math.random() < 0.5) {
    x = "hello";
    console.log(x);
    // let x: string;
  } else {
    x = 100;
    console.log(x);
    // let x: number;
  }

  return x;
  // let x: string | number;
}
```

## Using type predicates

`pet is Fish` is our _type predicate_ in this example.

```ts
function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}
```

A predicate takes the form `parameterName is Type`, where `parameterName` must be the name of a parameter from the current function signature.
