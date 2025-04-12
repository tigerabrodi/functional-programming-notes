## Understanding Functors

A functor is a wrapper or container type that lets you apply functions to the values inside without "opening" the container.

### What Makes Something a Functor?

To be a functor, a type needs:

1. To be a container or wrapper that holds values of some type
2. To have a `map` operation that:
   - Takes a function that transforms values from type A to type B
   - Returns a new container of the same shape, but with values transformed to type B

### The Map Operation Signature

For any functor `F`, the map operation looks like:

```typescript
map<A, B>(f: (a: A) => B, container: F<A>): F<B>
```

### Examples of Functors

1. **Array** is a functor:

```typescript
// The map operation for Array
const arrayMap = <A, B>(f: (a: A) => B, arr: A[]): B[] => {
  return arr.map(f);
};

// Usage
arrayMap((x) => x * 2, [1, 2, 3]); // [2, 4, 6]

// Or using the built-in method
[1, 2, 3].map((x) => x * 2); // [2, 4, 6]
```

2. **Option/Maybe** is a functor:

```typescript
type Option<A> = Some<A> | None;
type Some<A> = { tag: "some"; value: A };
type None = { tag: "none" };

const some = <A>(value: A): Option<A> => ({ tag: "some", value });
const none: Option<never> = { tag: "none" };

// The map operation for Option
const optionMap = <A, B>(f: (a: A) => B, opt: Option<A>): Option<B> => {
  if (opt.tag === "none") {
    return none;
  } else {
    return some(f(opt.value));
  }
};

// Usage
optionMap((x) => x * 2, some(3)); // { tag: 'some', value: 6 }
optionMap((x) => x * 2, none); // { tag: 'none' }
```

3. **Promise** is a functor:

```typescript
// The map operation for Promise (it's just .then)
const promiseMap = <A, B>(f: (a: A) => B, promise: Promise<A>): Promise<B> => {
  return promise.then(f);
};

// Usage
promiseMap((x) => x * 2, Promise.resolve(3)); // Promise that resolves to 6
```

## How `optionMap` Works

Let's examine `optionMap` step by step:

```typescript
const optionMap = <A, B>(f: (a: A) => B, opt: Option<A>): Option<B> => {
  if (opt.tag === "none") {
    // If we have None, return None without calling the function
    return none;
  } else {
    // If we have Some, apply the function to the value inside
    // and wrap the result in Some
    return some(f(opt.value));
  }
};
```

When we call `optionMap`:

```typescript
// Some example
const result1 = optionMap((x) => x * 2, some(3));

// How it evaluates:
// 1. opt.tag is 'some', so we go to the else branch
// 2. We apply f (which is x => x * 2) to opt.value (which is 3)
// 3. f(opt.value) gives us 6
// 4. We wrap 6 in Some: some(6)
// 5. Final result: { tag: 'some', value: 6 }

// None example
const result2 = optionMap((x) => x * 2, none);

// How it evaluates:
// 1. opt.tag is 'none', so we go to the if branch
// 2. We return none directly
// 3. Final result: { tag: 'none' }
```

The key is that `optionMap` knows how to handle both cases (`some` and `none`) appropriately without you having to check which case you have each time.

## Why Functors Are Useful

1. **Abstraction**: Hide the details of how to handle different container types
2. **Safety**: No need to manually check for special cases (like `null`)
3. **Composition**: Chain multiple transformations together
4. **Consistency**: Apply the same pattern across different container types
