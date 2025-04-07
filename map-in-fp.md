The key insight is that `map` is defined specifically for each container type, but follows a consistent pattern:

1. For Option:

```typescript
function map<A, B>(option: Option<A>, fn: (a: A) => B): Option<B> {
  return isNone(option) ? none : some(fn(option.value));
}
```

2. For Either:

```typescript
function map<E, A, B>(either: Either<E, A>, fn: (a: A) => B): Either<E, B> {
  return isLeft(either) ? either : right(fn(either.value));
}
```

3. For List:

```typescript
function map<A, B>(list: List<A>, fn: (a: A) => B): List<B> {
  if (isNil(list)) return nil;
  return cons(fn(list.head), map(list.tail, fn));
}
```

Each implementation respects:

- The structure of the container (Option stays Option, List stays List)
- The error/empty cases (None stays None, Left stays Left, Nil stays Nil)
- The transformation only applies to the "success" values

This consistent behavior across different data structures is what makes `map` so powerful in functional programming. It's why these container types are called "functors" - they all implement this mapping behavior that preserves structure while transforming content.
