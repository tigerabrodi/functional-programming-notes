## Pattern Matching Simplified

Pattern matching is basically "what kind of thing is this, and what's inside it?" It lets you:

1. Check which variant of a sum type you have
2. Extract the data from it
3. Do different things based on which variant it is

Think of it like an enhanced switch statement that can also unpack data.

Here's a simple example with our Option type:

```typescript
// Instead of this nested if approach:
function getDisplayName(userOption: Option<User>): string {
  if (userOption._tag === "None") {
    return "Guest";
  } else {
    // Extracting the value and using it
    const user = userOption.value;
    return user.name || "Unnamed User";
  }
}
```

We can create a helper function that makes this cleaner:

```typescript
function match<A, B>(
  option: Option<A>,
  handlers: {
    None: () => B;
    Some: (value: A) => B;
  }
): B {
  if (option._tag === "None") {
    return handlers.None();
  } else {
    return handlers.Some(option.value);
  }
}

// Now we can use it like this:
const displayName = match(userOption, {
  None: () => "Guest",
  Some: (user) => user.name || "Unnamed User",
});
```

This approach:

1. Is more declarative - you describe what should happen for each case
2. Forces you to handle all possibilities
3. Automatically unwraps the value for you to use
