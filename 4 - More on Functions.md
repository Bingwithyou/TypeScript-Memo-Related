# More on Functions

_More on reference link:_ <https://blog.csdn.net/yangxinxiang84/article/details/119490185>

## Function Type Expressions

The syntax `(a: string) => void` means “a function with one parameter, named `a`, of type `string`, that doesn’t have a return value”.

if a parameter type isn’t specified, it’s implicitly `any`.

```ts
function greeter(fn: (a: string) => void) {
  fn("Hello, World");
}

function printToConsole(s: string) {
  console.log(s);
}

greeter(printToConsole);
```

> Note that the parameter name is **required**. The function type (string) => void means "a function with a parameter named string of type any"!

Or use a type alias to name a function type:

```ts
type GreetFunction = (a: string) => void;
function greeter(fn: GreetFunction) {
  // ...
}
```

## Call Signatures

```ts
// This type defines a string parameter and a function that returns a boolean with someArg(number type) parameter.
type DescribableFunction = {
  description: string;

  // use ':' rather than '=>'
  (someArg: number): boolean;
};
function doSomething(fn: DescribableFunction) {
  console.log(fn.description + " returned " + fn(6));
}

function myFunc(someArg: number) {
  return someArg > 3;
}
myFunc.description = "default description";

doSomething(myFunc);
```

Note that the syntax is slightly different compared to a function type expression - use `:` between the parameter list and the return type rather than `=>`.
