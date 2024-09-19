# TypeSafe Pipeline Utility

## Introduction

This utility provides a type-safe way to create functional pipelines in JavaScript. It allows you to chain multiple functions together, where the output of each function becomes the input of the next function in the pipeline.

**Features:**
- **Type Safety**: Leverages TypeScript's type system to ensure type correctness throughout the pipeline.
- **Flexible**: Works with functions that take one argument and return a value.
- **Error Detection**: Catches type mismatches at compile-time, preventing runtime errors.

## Implementation Details

```ts
type AnyFunction = (...args: any[]) => any;

/**
 * Retrieves the return type of the last function in a tuple of functions.
 * If the tuple is empty, it returns the `Fallback` type.
 */
type LastFunctionReturnType<Functions extends AnyFunction[], Fallback = never> =
  Functions extends [...any[], (...args: any[]) => infer R] ? R : Fallback;

/**
 * Recursively builds an array of functions (`AccumulatedFunctions`) ensuring that
 * the output type of one function matches the input type of the next function.
 */
type PipeFunctions<
  Functions extends AnyFunction[],
  AccumulatedFunctions extends AnyFunction[] = []
> = Functions extends [(arg: infer Arg) => infer Ret, ...infer Rest]
  ? Rest extends [(arg: Ret) => any, ...any[]]
    ? PipeFunctions<Rest, [...AccumulatedFunctions, (arg: Arg) => Ret]>
    : Rest extends []
    ? [...AccumulatedFunctions, (arg: Arg) => Ret]
    : AccumulatedFunctions
  : AccumulatedFunctions;

/**
 * Composes functions from left to right, passing the result of each function to the next.
 * It ensures type safety by verifying that the output type of each function matches
 * the input type of the next function in the sequence.
 */
function pipe<FirstFunction extends AnyFunction, Functions extends AnyFunction[]>(
  arg: Parameters<FirstFunction>[0],
  firstFunction: FirstFunction,
  ...functions: PipeFunctions<Functions> extends Functions ? Functions : PipeFunctions<Functions>
): LastFunctionReturnType<Functions, ReturnType<FirstFunction>> {
  return (functions as AnyFunction[]).reduce((acc, fn) => fn(acc), firstFunction(arg));
}
```

## Usage

```ts
const result = pipe(
  "1",
  (a: string) => Number(a),
  (b: number) => b + 1,
  (c: number) => `${c}`,
  (d: string) => Number(d)
);

console.log(result); // Output: 2
```

### API

`pipe(initialValue, ...functions)`

- `initialValue`: The starting value for the pipeline
- `...functions`: A series of functions to be applied in order

Returns the result of applying all functions in sequence.

## Type Checking

The utility provides compile-time type checking:

```ts
// This will cause a TypeScript error
const invalid = pipe(
  "1",
  (a: string) => Number(a),
  (b: number) => "b + 1", // Error: Type 'string' is not assignable to type 'number'
  (c: number) => `${c}`,
  (d: string) => Number(d)
);
```

## How It Works

The utility uses advanced TypeScript features like conditional types and recursive type inference to ensure type safety throughout the pipeline.

## License

This project is licensed under the MIT License.

