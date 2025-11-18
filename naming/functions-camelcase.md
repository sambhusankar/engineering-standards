# Functions: camelCase with Verb Prefix

Use `camelCase` for function names, starting with a verb to describe the action.

**Note**: Functions use `camelCase`, while variables use `snake_case`.

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
function getUserProfile(user_id) { }
function validateEmail(email) { }
function formatCurrency(amount) { }
function parseApiResponse(data) { }
function calculateDiscount(price, percentage) { }
```

## Async Functions

Prefix clearly indicates async behavior:

```javascript
async function fetchUserData(user_id) {
  const response = await fetch(`/api/users/${user_id}`);
  return response.json();
}
```

## Contrast with Variables

Functions use `camelCase`, variables use `snake_case`:

```javascript
// Function: camelCase
function calculateTotal(item_list) {
  // Variables: snake_case
  const subtotal = item_list.reduce((sum, item) => sum + item.price, 0);
  const tax_rate = 0.08;
  const tax_amount = subtotal * tax_rate;
  const final_total = subtotal + tax_amount;

  return final_total;
}

const shopping_cart = [{ price: 10 }, { price: 20 }];
const total_price = calculateTotal(shopping_cart);
```

## Related Notes
- [Variables: snake_case](./variables-snake-case.md)
- [Arrow Functions vs Declarations](../architecture/arrow-vs-declaration.md)
