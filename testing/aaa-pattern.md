# Test Structure: Arrange-Act-Assert (AAA)

Structure all tests using the Arrange-Act-Assert pattern for clarity and consistency.

## The Pattern

**Arrange** - Set up test data and conditions
**Act** - Perform the action being tested
**Assert** - Verify the expected outcome

## Example

```javascript
test('should add item to cart', () => {
  // Arrange - Set up test data
  const cart = new ShoppingCart();
  const item = { id: 1, name: 'Widget', price: 10 };

  // Act - Perform the action
  cart.addItem(item);

  // Assert - Verify outcome
  expect(cart.items).toHaveLength(1);
  expect(cart.items[0]).toEqual(item);
  expect(cart.total).toBe(10);
});
```

## Why AAA

- **Readability**: Clear separation of setup, action, and verification
- **Consistency**: All tests follow same structure
- **Maintainability**: Easy to understand what test is checking

## Complex Example

```javascript
test('should calculate discount for multiple items', () => {
  // Arrange
  const cart = new ShoppingCart();
  const items = [
    { id: 1, price: 100 },
    { id: 2, price: 50 }
  ];
  const discountRate = 0.1; // 10% discount

  items.forEach(item => cart.addItem(item));

  // Act
  const total = cart.calculateTotal(discountRate);

  // Assert
  expect(total).toBe(135); // (100 + 50) * 0.9
});
```

## Related Notes
- [Test Naming Conventions](./test-naming.md)
- [Test Independence](./test-independence.md)
- [Setup and Teardown](./setup-teardown.md)
