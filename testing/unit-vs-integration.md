# Unit vs Integration Tests

Understand when to write unit tests vs integration tests following the testing pyramid.

## Testing Pyramid

```
      /\
     /  \  E2E (10%)
    /____\
   /      \
  /  Integ \ Integration (20%)
 /__________\
/            \
/ Unit Tests  \ Unit (70%)
/______________\
```

## Unit Tests

**Purpose**: Test individual functions, components, or modules in isolation

**When to write:**
- Utility functions
- Business logic
- Individual React components
- API service functions

**Example:**
```javascript
// Unit test - isolated function
test('calculateTotal should add tax to base price', () => {
  const result = calculateTotal(100, 0.08);
  expect(result).toBe(108);
});
```

**Characteristics:**
- Fast (milliseconds)
- No external dependencies
- Test one thing
- Easy to debug

## Integration Tests

**Purpose**: Test how multiple units work together

**When to write:**
- API endpoint workflows
- Database operations
- Component interactions
- Service layer integration

**Example:**
```javascript
// Integration test - multiple parts working together
test('should create and retrieve user', async () => {
  const userData = { name: 'John', email: 'john@example.com' };
  const created = await createUser(userData);

  const retrieved = await getUserById(created.id);
  expect(retrieved.name).toBe('John');
  expect(retrieved.email).toBe('john@example.com');
});
```

**Characteristics:**
- Slower (seconds)
- May use real database/services
- Test interactions
- Closer to production

## E2E Tests

**Purpose**: Test complete user workflows

**When to write:**
- Critical user journeys
- Complex multi-step workflows

**Example:**
```javascript
// E2E test - full user workflow
test('user can register and login', async ({ page }) => {
  await page.goto('/register');
  await page.fill('[name="email"]', 'test@example.com');
  await page.fill('[name="password"]', 'SecurePass123');
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL('/dashboard');
});
```

## Decision Guide

**Write unit test when:**
- Testing pure functions
- Logic can be isolated
- Need fast feedback
- Testing edge cases

**Write integration test when:**
- Testing workflows
- Multiple services interact
- Database involved
- API endpoints

**Write E2E test when:**
- Critical user paths
- Full stack verification needed
- Cross-system integration

## Related Notes
- [Testing Pyramid](./testing-pyramid.md)
- [Coverage Requirements](./coverage-requirements.md)
- [Mock External Dependencies](./mock-external-dependencies.md)
