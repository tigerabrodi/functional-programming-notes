# Understanding flatMap in Option Type

## Definition

```typescript
const flatMap =
  <T, U>(f: (value: T) => Option<U>) =>
  (option: Option<T>): Option<U> =>
    isNone(option) ? none : f(option.value);
```

## What flatMap Does

`flatMap` takes a function that:

- Accepts a raw value of type T
- Returns an Option<U>

When you call flatMap:

1. If your original option is None, flatMap returns None
2. If your original option is Some<T>, flatMap:
   - Extracts the raw value from inside the Option
   - Passes that raw value to your function
   - Returns whatever Option your function returns

## Why flatMap Is Needed

Without flatMap, when working with functions that return Options, you'd get nested Options:

- `Option<Option<T>>` - hard to work with!

flatMap "flattens" these nested structures, giving you a single Option.

## Practical Example

```typescript
// A function that might not find a user
const findUser = (id: number): Option<User> => {
  // Implementation that returns Some<User> or None
  const user = database.lookup(id);
  return user ? some(user) : none;
};

// A function that might not find an address
const findAddress = (user: User): Option<Address> => {
  return user.address ? some(user.address) : none;
};

// Without flatMap - results in nested Options
const wrongWay = (userId: number): Option<Option<Address>> => {
  const userOption = findUser(userId);
  // map creates nesting because findAddress already returns Option
  return map((user) => findAddress(user))(userOption);
};

// With flatMap - properly flattened
const rightWay = (userId: number): Option<Address> => {
  return flatMap((user) => findAddress(user))(findUser(userId));
};
```

## Chained Example

You can chain multiple flatMaps for operations that depend on each other:

```typescript
const getUserCity = (userId: number): Option<string> => {
  return flatMap((user) =>
    flatMap((address) => some(address.city))(findAddress(user))
  )(findUser(userId));
};

// The above avoids this nested structure:
// Option<Option<Option<string>>>
```

## The "Aha" Insight

The key to understanding flatMap is recognizing that:

1. It's for when your callback function ALREADY returns an Option
2. It handles unwrapping the outer Option for you
3. Your callback function receives the raw value, not an Option
4. The callback's return value (an Option) becomes the final result

Remember: Use `map` when your function returns a regular value. Use `flatMap` when your function returns an Option.
