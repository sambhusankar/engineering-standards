# Module Organization: Functional Modules

Organize code into functional modules rather than classes. Export related functions from a single file.

## Pattern

Group related functions in a module file:

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
 * Update existing user
 * @param {string} user_id
 * @param {Partial<User>} updates
 * @returns {Promise<User>}
 */
export async function updateUser(user_id, updates) {
  const response = await fetch(`/api/users/${user_id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(updates)
  });
  return response.json();
}

/**
 * Delete user
 * @param {string} user_id
 * @returns {Promise<void>}
 */
export async function deleteUser(user_id) {
  await fetch(`/api/users/${user_id}`, { method: 'DELETE' });
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

## Usage

Import only what you need:

```javascript
import { getUser, updateUser } from './userService.js';

async function updateUserProfile(user_id, new_name) {
  const user = await getUser(user_id);
  return await updateUser(user_id, { ...user, name: new_name });
}
```

Or import all exports as namespace:

```javascript
import * as userService from './userService.js';

const user = await userService.getUser('123');
await userService.updateUser('123', { name: 'New Name' });
```

## Module Structure Benefits

✅ **Simple** - Just functions, no `this` binding
✅ **Testable** - Each function can be tested independently
✅ **Tree-shakeable** - Import only what you use
✅ **Clear dependencies** - Function parameters show what's needed
✅ **No state** - Functions don't carry hidden state

## Organizing Utility Modules

Group related utilities:

```javascript
// dateUtils.js
export function formatDate(date) {
  return date.toISOString().split('T')[0];
}

export function addDays(date, days) {
  const result = new Date(date);
  result.setDate(result.getDate() + days);
  return result;
}

export function isWeekend(date) {
  const day_of_week = date.getDay();
  return day_of_week === 0 || day_of_week === 6;
}
```

```javascript
// stringUtils.js
export function capitalize(str) {
  return str.charAt(0).toUpperCase() + str.slice(1);
}

export function truncate(str, max_length) {
  return str.length > max_length ? str.slice(0, max_length) + '...' : str;
}

export function slugify(str) {
  return str.toLowerCase().replace(/\s+/g, '-');
}
```

## Module with Shared Configuration

If modules need shared setup, use closure pattern:

```javascript
// apiClient.js
let base_url = 'https://api.example.com';
let auth_token = null;

/**
 * Configure API client
 * @param {Object} config
 * @param {string} config.base_url
 * @param {string} [config.auth_token]
 */
export function configure(config) {
  base_url = config.base_url;
  auth_token = config.auth_token;
}

/**
 * Make GET request
 * @param {string} endpoint
 * @returns {Promise<any>}
 */
export async function get(endpoint) {
  const headers = {};
  if (auth_token) {
    headers.Authorization = `Bearer ${auth_token}`;
  }

  const response = await fetch(`${base_url}${endpoint}`, { headers });
  return response.json();
}

/**
 * Make POST request
 * @param {string} endpoint
 * @param {*} data
 * @returns {Promise<any>}
 */
export async function post(endpoint, data) {
  const headers = { 'Content-Type': 'application/json' };
  if (auth_token) {
    headers.Authorization = `Bearer ${auth_token}`;
  }

  const response = await fetch(`${base_url}${endpoint}`, {
    method: 'POST',
    headers,
    body: JSON.stringify(data)
  });
  return response.json();
}

// Usage:
// configure({ base_url: 'https://api.myapp.com', auth_token: 'abc123' });
// const data = await get('/users');
```

## Don't Use Classes

Instead of classes, use modules with exported functions:

```javascript
// ❌ Avoid - Class-based
class UserService {
  constructor(api_client) {
    this.apiClient = api_client;
  }

  async getUser(user_id) {
    return this.apiClient.get(`/users/${user_id}`);
  }
}

const service = new UserService(api_client);
const user = await service.getUser('123');

// ✅ Prefer - Functional module
export async function getUser(api_client, user_id) {
  return api_client.get(`/users/${user_id}`);
}

const user = await getUser(api_client, '123');

// Or if api_client is configured globally
let client = null;

export function configure(api_client) {
  client = api_client;
}

export async function getUser(user_id) {
  return client.get(`/users/${user_id}`);
}
```

## Related Notes
- [Functional Programming Principle](../principles/functional-programming.md)
- [Service Layer Pattern](./service-layer-pattern.md)
- [Functions: camelCase](../naming/functions-camelcase.md)
- [Named vs Default Exports](./named-vs-default-exports.md)
