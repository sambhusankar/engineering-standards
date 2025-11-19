# Functional Programming Approach

Prefer functional programming patterns over object-oriented programming for application code.

## Philosophy

For CRUD applications and typical business logic, functional programming provides:
- **Simplicity** - Functions are easier to understand and test than classes
- **Composability** - Small functions combine to build complex behavior
- **Immutability** - Reduce bugs by avoiding mutable state
- **Predictability** - Pure functions with no side effects are easier to reason about

## When to Use Functional vs OOP

### Use Functional Programming (Default)
- **CRUD operations** - Database queries, API endpoints
- **Business logic** - Calculations, transformations, validations
- **Data processing** - Mapping, filtering, reducing
- **Utilities** - Helper functions, formatters, parsers
- **React components** - Functional components with hooks

### Use OOP (Sparingly)
- **Very specific objects** - Date handling, specialized data structures
- **Third-party library integration** - When library requires classes
- **React Error Boundaries** - Only place classes are required in React

## Core Functional Patterns

### [Pure Functions](/architecture/functional/pure-functions.md)
Functions that always return the same output for the same input, with no side effects.

### [Function Composition](/architecture/functional/function-composition.md)
Build complex behavior from simple functions using composition and higher-order functions.

### [Immutability](/architecture/functional/immutability-patterns.md)
Avoid mutating data; create new data instead for predictability.

### [Array Functional Methods](/architecture/functional/array-methods-functional.md)
Use map, filter, reduce and other array methods for declarative transformations.

## Quick Examples

### Functional Module Pattern

```javascript
// userService.js - Functional module, not class

/**
 * @param {string} user_id
 * @returns {Promise<User>}
 */
export async function getUser(user_id) {
  const response = await fetch(`/api/users/${user_id}`);
  return response.json();
}

/**
 * @param {Partial<User>} user_data
 * @returns {Promise<User>}
 */
export async function createUser(user_data) {
  const response = await fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify(user_data)
  });
  return response.json();
}
```

### Immutable Data Transformations

```javascript
// Good - returns new array
function addItem(cart, item) {
  return [...cart, item];
}

// Good - returns new object
function updateUser(user, updates) {
  return { ...user, ...updates };
}
```

### Declarative Array Operations

```javascript
// Declarative - what to do
function getActiveUserNames(users) {
  return users
    .filter(user => user.active)
    .map(user => user.name)
    .sort();
}
```

## When Classes Are Acceptable

Very rare, but acceptable for:

```javascript
// React Error Boundaries (required by React)
class ErrorBoundary extends React.Component {
  // Only use of classes in React
}

// Custom error types
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}
```

## Related Notes
- [Module Organization](/architecture/modules/module-organization.md)
- [Service Layer Pattern](/architecture/modules/service-layer-pattern.md)
- [Error Boundaries](/architecture/error-boundaries.md)
- [JavaScript with JSDoc](/principles/javascript-with-jsdoc.md)
