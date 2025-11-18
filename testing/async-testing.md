# Async Testing: async/await Pattern

Always use async/await for testing asynchronous code. Avoid callbacks and promise chains.

## Good: async/await

```javascript
test('should fetch user data', async () => {
  const data = await fetchUserData('123');
  expect(data.name).toBe('John');
  expect(data.email).toBe('john@example.com');
});
```

## Bad: Callbacks or .then()

```javascript
// Don't do this - harder to read and maintain
test('should fetch user data', (done) => {
  fetchUserData('123').then(data => {
    expect(data.name).toBe('John');
    done();
  });
});
```

## Waiting for State Changes

Use `waitFor` from React Testing Library:

```javascript
import { waitFor } from '@testing-library/react';

test('should display loaded data', async () => {
  render(<UserProfile userId="123" />);

  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  });
});
```

## Testing Errors

```javascript
test('should handle fetch errors', async () => {
  fetchUserData.mockRejectedValue(new Error('Network error'));

  render(<UserProfile userId="123" />);

  await waitFor(() => {
    expect(screen.getByText(/failed to load/i)).toBeInTheDocument();
  });
});
```

## Multiple Async Operations

```javascript
test('should load multiple resources', async () => {
  const user = await fetchUser('123');
  const posts = await fetchUserPosts(user.id);
  const comments = await fetchPostComments(posts[0].id);

  expect(user).toBeDefined();
  expect(posts).toHaveLength(5);
  expect(comments).toHaveLength(3);
});
```

## Timeout Configuration

For slow operations, increase timeout:

```javascript
test('should complete long operation', async () => {
  const result = await longRunningOperation();
  expect(result).toBeDefined();
}, 10000); // 10 second timeout
```

## Related Notes
- [Testing React Components](./testing-react-components.md)
- [Mock External Dependencies](./mock-external-dependencies.md)
- [AAA Pattern](./aaa-pattern.md)
