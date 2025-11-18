# Utility Modules

Organize utility functions into focused modules grouped by purpose.

## Pattern: Grouped Utilities

Group related utilities in dedicated files:

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

## File Organization

```
src/utils/
├── dateUtils.js      # Date manipulation
├── stringUtils.js    # String operations
├── arrayUtils.js     # Array helpers
├── objectUtils.js    # Object transformations
└── validators.js     # Validation functions
```

## Usage

```javascript
import { formatDate, addDays } from '@/utils/dateUtils.js';
import { capitalize, truncate } from '@/utils/stringUtils.js';

const formatted_date = formatDate(new Date());
const next_week = addDays(new Date(), 7);
const title = capitalize('hello world');
```

## Object Transformation Utilities

```javascript
// objectUtils.js

/**
 * Pick specific properties from object
 * @param {Object} obj
 * @param {string[]} keys
 * @returns {Object}
 */
export function pick(obj, keys) {
  return keys.reduce((result, key) => {
    if (key in obj) {
      result[key] = obj[key];
    }
    return result;
  }, {});
}

/**
 * Omit specific properties from object
 * @param {Object} obj
 * @param {string[]} keys
 * @returns {Object}
 */
export function omit(obj, keys) {
  return Object.keys(obj).reduce((result, key) => {
    if (!keys.includes(key)) {
      result[key] = obj[key];
    }
    return result;
  }, {});
}

// Usage
const user = { id: 1, name: 'Alice', password: 'secret', email: 'alice@example.com' };
const safe_user = omit(user, ['password']);
```

## Keep Utilities Pure

Utilities should be pure functions when possible:

```javascript
// Good - pure function
export function calculateDiscount(price, percent) {
  return price * (1 - percent / 100);
}

// Avoid - side effects
let total = 0;
export function addToTotal(amount) {
  total += amount;  // Side effect
  return total;
}
```

## Related Notes
- [Module Exports Pattern](./module-exports-pattern.md)
- [Functional Programming](../../principles/functional-programming.md)
- [Pure Functions](../functional/pure-functions.md)
- [Files: Utilities](../../naming/files-utilities.md)
