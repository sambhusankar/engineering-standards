# Functional Programming Approach

Prefer functional programming patterns over object-oriented programming for most application code.

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

## Functional Patterns

### Pure Functions

Functions that always return same output for same input, with no side effects:

```javascript
// Pure function - good
function calculateTotal(items, taxRate) {
  const subtotal = items.reduce((sum, item) => sum + item.price, 0);
  return subtotal * (1 + taxRate);
}

// Impure - modifies external state
let total = 0;
function addToTotal(amount) {
  total += amount;  // Side effect - modifying external state
  return total;
}
```

### Function Composition

Build complex behavior from simple functions:

```javascript
// Small, focused functions
const add = (a, b) => a + b;
const multiply = (a, b) => a * b;
const subtract = (a, b) => a - b;

// Compose them
function calculateDiscount(price, discountPercent) {
  const discount = multiply(price, discountPercent / 100);
  return subtract(price, discount);
}

// Or use pipe/compose pattern
const pipe = (...fns) => (value) => fns.reduce((acc, fn) => fn(acc), value);

const processPrice = pipe(
  (price) => multiply(price, 0.9),  // 10% discount
  (price) => multiply(price, 1.08), // Add tax
  (price) => Math.round(price * 100) / 100  // Round to 2 decimals
);

console.log(processPrice(100)); // 97.2
```

### Immutability

Avoid mutating data; create new data instead:

```javascript
// Bad - mutates original array
function addItem(cart, item) {
  cart.push(item);  // Mutates cart
  return cart;
}

// Good - returns new array
function addItem(cart, item) {
  return [...cart, item];
}

// Bad - mutates object
function updateUser(user, updates) {
  user.name = updates.name;  // Mutates user
  return user;
}

// Good - returns new object
function updateUser(user, updates) {
  return { ...user, ...updates };
}
```

### Higher-Order Functions

Functions that take or return other functions:

```javascript
// Function that returns a function
function createMultiplier(factor) {
  return (number) => number * factor;
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15

// Function that takes a function
function applyOperation(numbers, operation) {
  return numbers.map(operation);
}

const numbers = [1, 2, 3, 4];
applyOperation(numbers, double);  // [2, 4, 6, 8]
applyOperation(numbers, triple); // [3, 6, 9, 12]
```

### Module Pattern (Not Classes)

Organize related functions in modules, not classes:

```javascript
// user-service.js - Functional module

/**
 * @typedef {Object} User
 * @property {string} id
 * @property {string} name
 * @property {string} email
 */

/**
 * @param {string} userId
 * @returns {Promise<User>}
 */
export async function getUser(userId) {
  const response = await fetch(`/api/users/${userId}`);
  return response.json();
}

/**
 * @param {Partial<User>} userData
 * @returns {Promise<User>}
 */
export async function createUser(userData) {
  const response = await fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify(userData)
  });
  return response.json();
}

/**
 * @param {string} userId
 * @param {Partial<User>} updates
 * @returns {Promise<User>}
 */
export async function updateUser(userId, updates) {
  const response = await fetch(`/api/users/${userId}`, {
    method: 'PUT',
    body: JSON.stringify(updates)
  });
  return response.json();
}

// Usage
import { getUser, updateUser } from './user-service.js';

const user = await getUser('123');
const updated = await updateUser('123', { name: 'New Name' });
```

### Declarative over Imperative

Describe what to do, not how:

```javascript
// Imperative - how to do it
function getActiveUserNames(users) {
  const names = [];
  for (let i = 0; i < users.length; i++) {
    if (users[i].active) {
      names.push(users[i].name);
    }
  }
  return names;
}

// Declarative - what to do
function getActiveUserNames(users) {
  return users
    .filter(user => user.active)
    .map(user => user.name);
}
```

## Functional Data Transformations

### Array Methods

Use built-in functional array methods:

```javascript
const users = [
  { id: 1, name: 'Alice', age: 30, active: true },
  { id: 2, name: 'Bob', age: 25, active: false },
  { id: 3, name: 'Charlie', age: 35, active: true }
];

// map - transform each item
const names = users.map(user => user.name);

// filter - select items
const activeUsers = users.filter(user => user.active);

// reduce - accumulate a value
const totalAge = users.reduce((sum, user) => sum + user.age, 0);

// find - get first match
const alice = users.find(user => user.name === 'Alice');

// some/every - test conditions
const hasInactive = users.some(user => !user.active);
const allActive = users.every(user => user.active);

// Chain them together
const activeUserNames = users
  .filter(user => user.active)
  .map(user => user.name)
  .sort();
```

### Object Transformations

```javascript
// Pick specific properties
function pick(obj, keys) {
  return keys.reduce((result, key) => {
    if (key in obj) {
      result[key] = obj[key];
    }
    return result;
  }, {});
}

// Omit specific properties
function omit(obj, keys) {
  return Object.keys(obj).reduce((result, key) => {
    if (!keys.includes(key)) {
      result[key] = obj[key];
    }
    return result;
  }, {});
}

const user = { id: 1, name: 'Alice', password: 'secret', email: 'alice@example.com' };
const safeUser = omit(user, ['password']);  // { id: 1, name: 'Alice', email: 'alice@example.com' }
```

## When Classes Might Be Needed

Very rare, but acceptable for:

```javascript
// Date handling libraries (already using classes)
const date = new Date();

// Error types (native JavaScript pattern)
class ValidationError extends Error {
  constructor(message, field) {
    super(message);
    this.name = 'ValidationError';
    this.field = field;
  }
}

// React Error Boundaries (required by React)
class ErrorBoundary extends React.Component {
  // Only use of classes in React
}
```

## Related Notes
- [Module Organization](../architecture/module-organization.md)
- [Service Layer Pattern](../architecture/service-layer-pattern.md)
- [Arrow Functions vs Declarations](../architecture/arrow-vs-declaration.md)
- [Pure Functions and Side Effects](../architecture/pure-functions.md)
