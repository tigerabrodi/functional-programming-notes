```typescript
const processUser = (user) =>
  user ? pipe(getNameOrDefault, createGreeting, shout)(user) : "No user";
```

## How `pipe` Works

1. `pipe` takes multiple functions as arguments: `pipe(f1, f2, f3, ...)`
2. It returns a new function that will:
   - Take an initial input value
   - Pass that value to the first function (`f1`)
   - Take the result and pass it to the second function (`f2`)
   - Take that result and pass it to the third function (`f3`)
   - And so on until all functions are applied
   - Return the final result

## Step-by-Step Execution

In our example:

```typescript
pipe(getNameOrDefault, createGreeting, shout)(user);
```

If we were to call this with `user = { name: "Alice" }`, here's what happens:

1. `user` is passed to `getNameOrDefault`

   - `getNameOrDefault({ name: "Alice" })` returns `"Alice"`

2. The result `"Alice"` is passed to `createGreeting`

   - `createGreeting("Alice")` returns `"Hello, Alice!"`

3. The result `"Hello, Alice!"` is passed to `shout`

   - `shout("Hello, Alice!")` returns `"HELLO, ALICE!"`

4. The final result `"HELLO, ALICE!"` is returned from the pipe

## Implementation of `pipe`

If you're curious how `pipe` itself is implemented, here's a simple version:

```typescript
function pipe(...fns) {
  return function (initialValue) {
    return fns.reduce((value, fn) => fn(value), initialValue);
  };
}
```

This implementation:

1. Takes any number of functions (`...fns`)
2. Returns a new function that takes an initial value
3. Uses `reduce` to apply each function in sequence, starting with the initial value
4. Returns the final result

## Why Use `pipe`?

The main benefits of using `pipe` are:

1. **Readability**: Functions are read in the same order they're applied (left to right)
2. **Composability**: You can easily add, remove, or reorder transformations
3. **Reusability**: Each step can be a standalone function used elsewhere
4. **Testability**: Each step can be tested independently

It's especially useful for creating data transformation pipelines that are easy to understand and modify.
