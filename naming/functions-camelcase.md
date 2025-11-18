# Functions: camelCase with Verb Prefix

Use `camelCase` for function names, starting with a verb to describe the action.

## Pattern

```javascript
function fetchUserData() { }
function calculateTotalPrice() { }
function isValidEmail(email) { }
async function getUserById(id) { }
```

## Common Verb Prefixes

**Data operations:**
- `get` / `fetch` - Retrieve data
- `set` / `update` - Modify data
- `create` / `add` - Create new data
- `delete` / `remove` - Remove data

**Validation/checking:**
- `is` / `has` - Boolean checks
- `validate` - Validation logic
- `check` - Verification

**Processing:**
- `calculate` - Computations
- `process` - Data transformation
- `format` - Formatting operations
- `parse` - Parsing logic

## Examples

```javascript
function getUserProfile(userId) { }
function validateEmail(email) { }
function formatCurrency(amount) { }
function parseApiResponse(data) { }
function calculateDiscount(price, percentage) { }
```

## Async Functions

Prefix clearly indicates async behavior:

```javascript
async function fetchUserData(id) {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}
```

## Related Notes
- [Variables: camelCase](./variables-camelcase.md)
- [Arrow Functions vs Declarations](../architecture/arrow-vs-declaration.md)
