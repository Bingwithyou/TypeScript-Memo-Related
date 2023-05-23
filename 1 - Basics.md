# The Basics

## Static type-checking

Find bugs before our code runs, that’s what a static type-checker like TypeScript does. Static types systems describe the shapes and behaviors of what our values will be when we run our programs.

```ts
const message = "hello!";

message();
// error:
// This expression is not callable.
//   Type 'String' has no call signatures.
```

## Non-exception Failures

When accessing a propetry that doesn't exist on an object, JavaScript returns the value `undefined`:

```js
const user = {
  name: "Daniel",
  age: 26,
};
user.location; // returns undefined
```

In TypeScript, the following code produces an error about location not being defined:

```js
const user = {
  name: "Daniel",
  age: 26,
};
user.location;
// error: Property 'location' does not exist on type '{ name: string; age: number; }'.
```

TypeScript can find typos and basic logic errors

## tsc, the TypeScirpt compiler

tsc, the TypeScript compiler. First we’ll need to grab it via npm.

`npm install -g typescript`

use `tsc xxx.ts` to check if your TypeScript program have type errors

- Will nothing happen if there is no errors, generating updated JS file anyway;

- use `tsc --noEmitOnError xxx.ts` to prevent update generated JS file when error occur;

## Explicit Types

Add type annotations on person and date to describe what types of values greet can be called with, and will show error message if called incorrectly:

```ts
function greet(person: string, date: Date) {
  console.log(`Hello ${person}, today is ${date.toDateString()}!`);
}

// error: note should be use 'new Date()' instead Date() as it will generate a string
greet("Maddison", new Date());
```

## Erased Types and Downleveling

1. Our person and date parameters no longer have type annotations.
2. Our "template string" - that string that used backticks (the ` character) - was converted to plain strings with concatenations.

```js
"use strict";
function greet(person, date) {
  console.log(
    "Hello ".concat(person, ", today is ").concat(date.toDateString(), "!")
  );
}
greet("Maddison", new Date());
```

Why did this happen (why use concat)?

Template strings are a feature from a version of ECMAScript called ECMAScript 2015, TypeScript has the ability to rewrite code from newer versions of ECMAScript to older ones such as ECMAScript 3 or ECMAScript 5.

> Default TypeScript targets ES3

This process of moving from a newer or “higher” version of ECMAScript down to an older or “lower” one is sometimes called downleveling.

> use `tsc --target es2015 xxx.ts` give us newer version of ECMASript.

## Strictness

TypeScript has several type-checking strictness flags that can be turned on or off.

### noImplicitAny

Turning on the noImplicitAny flag will issue an error on any variables whose type is implicitly inferred as any.

```ts
function fn(s) {
  // error: Parameter 's' implicitly has an 'any' type.
  console.log(s.subtr(3));
}
```

> Using `any` often defeats the purpose of using TypeScript in the first place.

### strictNullChecks

The strictNullChecks flag makes handling null and undefined more explicit, and spares us from worrying about whether we forgot to handle null and undefined.

```ts
// Setting strictNullChecks to true will raise an error that you have not made a guarantee that the loggedInUser exists before trying to use it.
declare const loggedInUsername: string;

const users = [
  { name: "Oby", age: 12 },
  { name: "Heera", age: 32 },
];

const loggedInUser = users.find((u) => u.name === loggedInUsername);
console.log(loggedInUser.age);
// error: 'loggedInUser' is possibly 'undefined'.
```
