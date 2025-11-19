# Test Data: Use Factories

Create test data using factory functions for consistency and maintainability.

## Factory Pattern

```javascript
// testUtils/factories.js
export function createUser(overrides = {}) {
  return {
    id: Math.random().toString(),
    name: 'Test User',
    email: 'test@example.com',
    role: 'user',
    createdAt: new Date(),
    ...overrides,
  };
}

export function createProduct(overrides = {}) {
  return {
    id: Math.random().toString(),
    name: 'Test Product',
    price: 99.99,
    inStock: true,
    ...overrides,
  };
}
```

## Usage in Tests

```javascript
import { createUser, createProduct } from './testUtils/factories';

test('should calculate discount for admin user', () => {
  const admin = createUser({ role: 'admin' });
  const product = createProduct({ price: 100 });

  const discount = calculateDiscount(admin, product);
  expect(discount).toBe(20); // 20% admin discount
});

test('should handle out of stock products', () => {
  const product = createProduct({ inStock: false });

  expect(() => addToCart(product)).toThrow('Product out of stock');
});
```

## Benefits

- **Consistency**: All tests use same baseline data
- **Maintainability**: Update factory once, all tests get update
- **Flexibility**: Override specific fields as needed
- **Readability**: Clear what's being tested vs default data

## Complex Factories

```javascript
export function createOrder(overrides = {}) {
  const default_values = {
    id: Math.random().toString(),
    user: createUser(),
    items: [createProduct(), createProduct()],
    status: 'pending',
    total: 199.98,
    createdAt: new Date(),
  };

  return {
    ...default_values,
    ...overrides,
  };
}
```

## Related Notes
- [Test Independence](/testing/test-independence.md)
- [Setup and Teardown](/testing/setup-teardown.md)
- [AAA Pattern](/testing/aaa-pattern.md)
