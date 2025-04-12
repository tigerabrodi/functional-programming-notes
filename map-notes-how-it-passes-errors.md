```typescript
// The Either type
type Either<E, A> = Left<E> | Right<A>;

// Left variant
type Left<E> = { tag: "left"; value: E };
function left<E>(value: E): Either<E, never> {
  return { tag: "left", value };
}

// Right variant
type Right<A> = { tag: "right"; value: A };
function right<A>(value: A): Either<never, A> {
  return { tag: "right", value };
}

// The map implementation
function map<E, A, B>(f: (a: A) => B, either: Either<E, A>): Either<E, B> {
  if (either.tag === "right") {
    // If it's a Right, apply the function to the value
    return right(f(either.value));
  } else {
    // If it's a Left, just return it unchanged
    return either;
  }
}

// The mapLeft implementation
function mapLeft<E, F, A>(f: (e: E) => F, either: Either<E, A>): Either<F, A> {
  if (either.tag === "left") {
    // If it's a Left, apply the function to the error value
    return left(f(either.value));
  } else {
    // If it's a Right, just return it unchanged
    return either;
  }
}
```

So to answer your question directly:

1. If `map` is called on a Left value, it simply returns the Left unchanged (forwards it)
2. If `mapLeft` is called on a Right value, it simply returns the Right unchanged (forwards it)

This is why in our example, the entire chain works on an Either value:

- The success path operations will only run if we have a Right
- The error path operations will only run if we have a Left
- Each operation in the chain respects this separation

This pattern creates essentially two parallel processing tracks in your code - one for success cases and one for errors - that never interfere with each other.
