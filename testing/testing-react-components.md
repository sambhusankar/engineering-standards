# Testing React Components: React Testing Library

Use React Testing Library to test components by simulating user interactions.

## Core Principle

Test components the way users interact with them, not implementation details.

## Basic Example

```javascript
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from './LoginForm';

test('should submit form with user input', async () => {
  const onSubmit = jest.fn();
  render(<LoginForm onSubmit={onSubmit} />);

  // Interact as user would
  await userEvent.type(screen.getByLabelText('Email'), 'test@example.com');
  await userEvent.type(screen.getByLabelText('Password'), 'password123');
  await userEvent.click(screen.getByRole('button', { name: /submit/i }));

  await waitFor(() => {
    expect(onSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123',
    });
  });
});
```

## Query Priority

Prefer queries that reflect how users find elements:

1. `getByRole` - Accessible to everyone
2. `getByLabelText` - Form fields
3. `getByPlaceholderText` - Alternative for inputs
4. `getByText` - Non-interactive elements
5. `getByTestId` - Last resort

```javascript
// Good - accessible queries
screen.getByRole('button', { name: /submit/i });
screen.getByLabelText('Email');
screen.getByText('Welcome');

// Avoid - implementation details
screen.getByClassName('btn-primary');
container.querySelector('.email-input');
```

## Async Operations

Use `waitFor` for async state changes:

```javascript
test('should load and display user data', async () => {
  render(<UserProfile userId="123" />);

  // Initially shows loading
  expect(screen.getByText(/loading/i)).toBeInTheDocument();

  // Wait for data to load
  await waitFor(() => {
    expect(screen.getByText('John Doe')).toBeInTheDocument();
  });
});
```

## Testing User Interactions

Use `userEvent` for realistic interactions:

```javascript
import userEvent from '@testing-library/user-event';

test('should toggle dropdown on click', async () => {
  render(<Dropdown />);

  const button = screen.getByRole('button');
  await userEvent.click(button);

  expect(screen.getByRole('menu')).toBeVisible();
});
```

## Related Notes
- [Mock External Dependencies](./mock-external-dependencies.md)
- [AAA Pattern](./aaa-pattern.md)
- [Async Testing](./async-testing.md)
