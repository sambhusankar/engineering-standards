# Module Exports Pattern

Organize code into functional modules using named exports rather than classes.

## Pattern: Named Exports

Export related functions from a single module file:

```javascript
// userService.js

/**
 * @typedef {Object} User
 * @property {string} id
 * @property {string} name
 * @property {string} email
 * @property {boolean} active
 */

/**
 * Get user by ID
 * @param {string} user_id
 * @returns {Promise<User>}
 */
export async function getUser(user_id) {
  const response = await fetch(`/api/users/${user_id}`);
  if (!response.ok) {
    throw new Error('User not found');
  }
  return response.json();
}

/**
 * Create new user
 * @param {Partial<User>} user_data
 * @returns {Promise<User>}
 */
export async function createUser(user_data) {
  const response = await fetch('/api/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(user_data)
  });
  return response.json();
}

/**
 * List all active users
 * @returns {Promise<User[]>}
 */
export async function listActiveUsers() {
  const all_users = await listUsers();
  return all_users.filter(user => user.active);
}

// Private helper function (not exported)
async function listUsers() {
  const response = await fetch('/api/users');
  return response.json();
}
```

## Usage: Named Imports

Import only what you need:

```javascript
import { getUser, updateUser } from './userService.js';

async function updateUserProfile(user_id, new_name) {
  const user = await getUser(user_id);
  return await updateUser(user_id, { ...user, name: new_name });
}
```

## Usage: Namespace Imports

Or import all exports as a namespace:

```javascript
import * as userService from './userService.js';

const user = await userService.getUser('123');
await userService.updateUser('123', { name: 'New Name' });
```

## Benefits

✅ **Simple** - Just functions, no `this` binding
✅ **Testable** - Each function can be tested independently
✅ **Tree-shakeable** - Import only what you use
✅ **Clear dependencies** - Function parameters show what's needed
✅ **No state** - Functions don't carry hidden state

## Avoid Default Exports

Prefer named exports for consistency:

```javascript
// Good - named export
export function getUser(user_id) { }

// Avoid - default export
export default function(user_id) { }
```

## Related Notes
- [Service Layer Pattern](/architecture/modules/service-layer-pattern.md)
- [Utility Modules](/architecture/modules/utility-modules.md)
- [Module Shared State](/architecture/modules/module-shared-state.md)
- [Functional Programming](/principles/functional-programming.md)
