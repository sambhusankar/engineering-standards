# Test Naming: Descriptive Sentences

Use clear, descriptive test names that read like sentences describing behavior.

## Pattern

Use `describe` for nouns (what you're testing) and `it` for verbs (what it should do):

```javascript
describe('LoginForm', () => {
  it('validates email format', () => { });
  it('shows error for wrong password', () => { });
  it('redirects after successful login', () => { });
});
```

## Good Examples

```javascript
it('should show error message when email is invalid', () => { });
it('should disable submit button while form is submitting', () => { });
it('should redirect to login page when user is not authenticated', () => { });
```

## Bad Examples

```javascript
// Too vague
it('works', () => { });
it('test email validation', () => { });
it('handles edge case', () => { });

// Too technical
it('calls handleSubmit with correct args', () => { });
it('sets state.loading to true', () => { });
```

## Nested Describe Blocks

Organize related tests:

```javascript
describe('ShoppingCart', () => {
  describe('addItem', () => {
    it('should add item to empty cart', () => { });
    it('should increment quantity for existing item', () => { });
    it('should throw error for invalid item', () => { });
  });

  describe('removeItem', () => {
    it('should remove item from cart', () => { });
    it('should handle removing non-existent item', () => { });
  });
});
```

## Test Output Readability

Good names create readable test output:

```
ShoppingCart
  addItem
    ✓ should add item to empty cart
    ✓ should increment quantity for existing item
    ✓ should throw error for invalid item
```

## Related Notes
- [AAA Pattern](./aaa-pattern.md)
- [Test Organization](./test-organization.md)
- [Test File Naming](../naming/files-tests.md)
