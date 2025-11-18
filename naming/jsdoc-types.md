# JSDoc Type Patterns

Document types and function signatures using JSDoc comments in JavaScript.

## Basic Type Annotations

```javascript
/**
 * @param {string} name - User's name
 * @param {number} age - User's age
 * @param {boolean} isActive - Whether user is active
 * @returns {Object} User object
 */
function createUser(name, age, isActive) {
  return { name, age, isActive };
}
```

## Type Definitions

Define reusable types with @typedef:

```javascript
/**
 * @typedef {Object} User
 * @property {string} id - User ID
 * @property {string} name - Full name
 * @property {string} email - Email address
 * @property {'admin'|'user'|'guest'} role - User role
 */

/**
 * @param {User} user
 * @returns {boolean}
 */
function isAdmin(user) {
  return user.role === 'admin';
}
```

## Complex Object Types

```javascript
/**
 * @typedef {Object} ApiResponse
 * @property {boolean} success - Whether request succeeded
 * @property {*} data - Response data (any type)
 * @property {string} [error] - Error message (optional)
 * @property {number} statusCode - HTTP status code
 */

/**
 * @param {string} url
 * @returns {Promise<ApiResponse>}
 */
async function fetchData(url) {
  const response = await fetch(url);
  return {
    success: response.ok,
    data: await response.json(),
    statusCode: response.status
  };
}
```

## Array and Union Types

```javascript
/**
 * @param {string[]} tags - Array of tag strings
 * @param {(string|number)} id - Can be string or number
 * @param {Array<User>} users - Array of User objects
 */
function processTags(tags, id, users) {
  // Implementation
}
```

## Generic Types with Template

```javascript
/**
 * Generic list wrapper
 * @template T
 * @typedef {Object} List
 * @property {T[]} items - Array of items
 * @property {number} count - Item count
 */

/**
 * @template T
 * @param {T[]} items
 * @returns {List<T>}
 */
function createList(items) {
  return {
    items,
    count: items.length
  };
}
```

## Function Type Documentation

```javascript
/**
 * @callback FilterFunction
 * @param {*} item - Item to filter
 * @param {number} index - Item index
 * @returns {boolean} Whether to keep item
 */

/**
 * @param {Array} items
 * @param {FilterFunction} filterFn
 * @returns {Array}
 */
function filterItems(items, filterFn) {
  return items.filter(filterFn);
}
```

## Importing Types from Other Files

```javascript
/**
 * @typedef {import('./user').User} User
 */

/**
 * @param {User} user
 */
function processUser(user) {
  // Can reference User type from another file
}
```

## Module Exports with JSDoc

Document exported functions in modules:

```javascript
/**
 * @module user-service
 */

/**
 * Get user by ID
 * @param {string} id - User ID
 * @returns {Promise<User>}
 */
export async function getUser(id) {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

/**
 * Create new user
 * @param {Partial<User>} userData - User data
 * @returns {Promise<User>}
 */
export async function createUser(userData) {
  const response = await fetch('/api/users', {
    method: 'POST',
    body: JSON.stringify(userData)
  });
  return response.json();
}
```

## Optional Type Checking

Enable type checking in specific files:

```javascript
// @ts-check

/**
 * @param {string} name
 * @returns {string}
 */
function greet(name) {
  return `Hello ${name}`;
}

// Editor will warn: Argument of type 'number' not assignable to 'string'
greet(123);
```

## Related Notes
- [JavaScript with JSDoc Principle](../principles/javascript-with-jsdoc.md)
- [Functions: camelCase](./functions-camelcase.md)
- [Module Organization](../architecture/module-organization.md)
