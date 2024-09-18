*This document provides an overview of the `pipe` function, which allows for chaining multiple functions with the same arguments. The function takes any number of callbacks and returns a new function that, when called, executes each callback in the order they were provided.*

## Function: `pipe`

### Description

The `pipe` function calls all functions in the order they were chained with the same arguments. It facilitates function composition by allowing the output of one function to be used as input for the next.

### Implementation Details

```ts
/**
 * Calls all functions in the order they were chained with the same arguments.
 */
export function pipe(...callbacks: unknown[]): (...args: unknown[]) => void {
  return (...args: unknown[]) => {
    for (const callback of callbacks) {
      if (typeof callback === 'function') {
        callback(...args)
      }
    }
  }
}
```

### Example

```ts
const log = (message: string) => console.log(message);
const alert = (message: string) => window.alert(message);

const pipedFunction = pipe(log, alert);
pipedFunction("Hello, World!");
```

In this example, the message `Hello, World!` will be logged to the console and then alerted in a pop-up.
