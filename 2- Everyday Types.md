# Everyday Types

## Type Annotations on Variables

When you declare a variable using `const`, `var`, or `let`, you can optionally add a type annotation to explicitly specify the type of the variable:

```ts
let myName: string = "Alice";`
```

> TypeScript doesn’t use “types on the left”-style declarations like int x = 0; Type annotations will always go _after_ the thing being typed.

In most cases, though, this isn’t needed. Wherever possible, TypeScript tries to automatically `infer` the types in your code.

```ts
// No type annotation needed -- 'myName' inferred as type 'string'
let myName = "Alice";
```

## Functions

TypeScript allows you to specify the types of both the input and output values of functions.

### Parameter Type Annotations

Add type annotations after each parameter to declare what types of parameters the function accepts.

```ts
// Parameter type annotation
function greet(name: string) {
  console.log("Hello, " + name.toUpperCase() + "!!");
}

// Would be a runtime error if executed!
greet(42);
// error: Argument of type  'number' is not assignable to parameter of type 'string'.
```

> Even if you don’t have type annotations on your parameters, TypeScript will still check that you passed the right number of arguments.

### Return Type Annotations

Add return type annotations, appear after the parameter list:

```ts
function getFavoriteNumber(): number {
  return 26;
}
```

Much like variable type annotations, you usually don’t need a return type annotation because TypeScript will infer the function’s return type based on its `return` statements.

### Anonymous Functions

When a function appears in a place where TypeScript can determine how it’s going to be called, the parameters of that function are automatically given types.

```ts
// No type annotations here, but TypeScript can spot the bug
const names = ["Alice", "Bob", "Eve"];

// Contextual typing for function
names.forEach(function (s) {
  console.log(s.toUppercase());
  // error: Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
});

// Contextual typing also applies to arrow functions
names.forEach((s) => {
  console.log(s.toUppercase());
  // error: Property 'toUppercase' does not exist on type 'string'. Did you mean 'toUpperCase'?
});
```

Even though the parameter `s` didn’t have a type annotation, TypeScript used the types of the `forEach` function, along with the inferred type of the array, to determine the type `s` will have.

## Object Types

To define an object type, we simply list its properties and their types.
If you don’t specify a type, it will be assumed to be `any`.

```ts
// The parameter's type annotation is an object type
// or we can use ',' to separate the properties: pt: { x: number, y: number }
function printCoord(pt: { x: number; y: number }) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
printCoord({ x: 3, y: 7 });
```

### Optional Properties

Add a `?` after the property name to specify this properties are `optional`.

```ts
function printName(obj: { first: string; last?: string }) {
  // ...
}
// Both OK
printName({ first: "Bob" });
printName({ first: "Alice", last: "Alisson" });
```

When you read from an optional property, you’ll have to check for `undefined` before using it.

```ts
function printName(obj: { first: string; last?: string }) {
  // Error - might crash if 'obj.last' wasn't provided!
  console.log(obj.last.toUpperCase());
  // error: 'obj.last' is possibly 'undefined'.

  if (obj.last !== undefined) {
    // OK
    console.log(obj.last.toUpperCase());
  }

  // A safe alternative using modern JavaScript syntax:
  console.log(obj.last?.toUpperCase());
}
```

## Union Types

### Defining a Union Type

A union type is a type formed from two or more other types, representing values that may be any one of those types.
Refer to each of these types as the union’s _members_.

```ts
// function that can operate on strings or numbers:
function printId(id: number | string) {
  console.log("Your ID is: " + id);
}
// OK
printId(101);
// OK
printId("202");
// Error
printId({ myID: 22342 });
// Argument of type '{ myID: number; }' is not assignable to parameter of type 'string | number'.
```

### Working with Union Types

TypeScript will only allow an operation if it is valid for every member of the union.

For example, if you have the union `string | number`, you can’t use methods that are only available on `string`:

```ts
function printId(id: number | string) {
  console.log(id.toUpperCase());
  // Property 'toUpperCase' does not exist on type 'string | number'.
  //   Property 'toUpperCase' does not exist on type 'number'.
}
```

The solution is to _narrow_ the union with code.
Narrowing occurs when TypeScript can deduce a more specific type for a value based on the structure of the code.

For example, TypeScript knows that only a `string` value will have a `typeof` value `"string"`:

```ts
function printId(id: number | string) {
  if (typeof id === "string") {
    // In this branch, id is of type 'string'
    console.log(id.toUpperCase());
  } else {
    // Here, id is of type 'number'
    console.log(id);
  }
}
```

> Notice that in the `else` branch, we don’t need to do anything special - if x wasn’t a `string[]`, then it must have been a `string`.

If every member in a union has property in common, you can use that property without narrowing.

```ts
// Return type is inferred as number[] | string
// both arrays and strings have a 'slice' method.
function getFirstThree(x: number[] | string) {
  return x.slice(0, 3);
}
```

## Type Aliases

A _type alias_ is exactly that - a name for any _type_. The syntax for a type alias is:

```ts
type Point = {
  x: number;
  y: number;
};

// Exactly the same as the earlier example
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}

printCoord({ x: 100, y: 100 });

// Use type alias name a union type:
type ID = number | string;
```

## Interfaces

An _interface_ declaration is another way to name an object type:

```ts
interface Point {
  x: number;
  y: number;
}

function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}

printCoord({ x: 100, y: 100 });
```

TypeScript is only concerned with the structure of the value we passed to `printCoord` - it only cares that it has the expected properties.

Being concerned only with the structure and capabilities of types is why we call TypeScript a _structurally typed_ type system.

### Differences Between Type Aliases and Interfaces

- Almost all features of an `interface` are available in `type`;

- Key distinction is that a type cannot be re-opened to add new properties vs an interface which is always extendable.

For the most part, you can choose based on personal preference, and TypeScript will tell you if it needs something to be the other kind of declaration. If you would like a heuristic, use `interface` until you need to use features from `type`.
