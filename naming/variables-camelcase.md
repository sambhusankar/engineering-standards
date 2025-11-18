# Variables: camelCase

Use `camelCase` for variable names in JavaScript.

## Pattern

Start with lowercase letter, capitalize first letter of each subsequent word:

```javascript
const userName = "Alex";
const maxRetryCount = 3;
let isLoading = false;
const apiBaseUrl = "https://api.example.com";
```

## When to Use

- Local variables
- Function parameters
- Object properties
- Most constant values (see exception below)

## Exception

Use `SCREAMING_SNAKE_CASE` for true constants that are configuration values:

```javascript
const MAX_UPLOAD_SIZE = 5242880; // Configuration constant
const currentUser = getCurrentUser(); // Regular variable
```

## Related Notes
- [Boolean Variables Prefix](./boolean-prefix.md)
- [Constants: SCREAMING_SNAKE_CASE](./constants-screaming-snake.md)
- [Functions: camelCase](./functions-camelcase.md)
