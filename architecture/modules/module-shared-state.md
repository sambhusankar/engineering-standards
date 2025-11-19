# Module Shared State

Use closure pattern for modules that need shared configuration or state.

## Pattern: Module-Level State

When modules need shared setup, use module-level variables with a configure function:

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
```

## Usage

```javascript
import { configure, get, post } from './apiClient.js';

// Configure once at app startup
configure({
  base_url: 'https://api.myapp.com',
  auth_token: 'abc123'
});

// Use throughout the app
const data = await get('/users');
await post('/users', { name: 'Alice' });
```

## When to Use Module State

**Use for:**
- API client configuration
- Feature flags
- Application-wide settings
- Singleton-like services

**Don't use for:**
- User-specific data (use parameters)
- Component-specific state (use React state)
- Frequently changing data (pass as parameters)

## Alternative: Dependency Injection

For better testability, consider passing dependencies as parameters:

```javascript
// Instead of module state
export async function getUser(api_client, user_id) {
  return api_client.get(`/users/${user_id}`);
}

// Usage
const api_client = createApiClient({ base_url: '...' });
const user = await getUser(api_client, '123');
```

## Related Notes
- [Module Exports Pattern](/architecture/modules/module-exports-pattern.md)
- [Service Layer Pattern](/architecture/modules/service-layer-pattern.md)
- [Functional Programming](/principles/functional-programming.md)
