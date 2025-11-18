# Constants: SCREAMING_SNAKE_CASE

Use `SCREAMING_SNAKE_CASE` for true constants - configuration values that never change.

## Pattern

All uppercase letters with underscores separating words:

```javascript
const API_BASE_URL = "https://api.example.com";
const MAX_UPLOAD_SIZE = 5242880; // 5MB in bytes
const DEFAULT_TIMEOUT = 30000;
const ERROR_MESSAGES = {
  NOT_FOUND: 'Resource not found',
  UNAUTHORIZED: 'Access denied'
};
```

## When to Use

Use for:
- Configuration values
- Magic numbers that need names
- Environment-based constants
- Enum-like values

## When NOT to Use

Don't use for regular variables, even if they use `const`:

```javascript
// Wrong - this is a regular variable
const USER_DATA = fetchUser();

// Right - this is a configuration constant
const MAX_RETRIES = 3;

// Right - regular variable
const userData = fetchUser();
```

## Distinction

```javascript
// Configuration constant - use SCREAMING_SNAKE_CASE
const API_TIMEOUT = 10000;

// Computed value - use camelCase even with const
const calculatedTotal = items.reduce((sum, item) => sum + item.price, 0);
```

## Related Notes
- [Variables: camelCase](./variables-camelcase.md)
- [Enums Pattern](./enums-pattern.md)
