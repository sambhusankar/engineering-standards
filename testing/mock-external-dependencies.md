# Mock External Dependencies, Not Internal Implementation

Mock external APIs and services, but avoid mocking internal implementation details.

## Good: Mock External APIs

```javascript
import { fetchUserData } from './api';
jest.mock('./api');

test('should display user data', async () => {
  // Mock external API call
  fetchUserData.mockResolvedValue({ name: 'John', email: 'john@example.com' });

  const component = render(<UserProfile userId="123" />);

  await waitFor(() => {
    expect(component.getByText('John')).toBeInTheDocument();
  });
});
```

## Bad: Mock Internal Implementation

```javascript
// Don't do this - too coupled to implementation
import { UserComponent } from './UserComponent';
jest.spyOn(UserComponent.prototype, 'handleClick');

test('should call handleClick', () => {
  // This test breaks if implementation changes
  const component = render(<UserComponent />);
  component.find('button').click();
  expect(UserComponent.prototype.handleClick).toHaveBeenCalled();
});
```

## What to Mock

**Do mock:**
- External API calls
- Database queries
- File system operations
- Network requests
- Third-party services

**Don't mock:**
- Internal functions of the module being tested
- Implementation details
- Private methods

## Using MSW for API Mocking

```javascript
import { rest } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  rest.get('/api/users/:id', (req, res, ctx) => {
    return res(ctx.json({ id: req.params.id, name: 'John' }));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());
```

## Related Notes
- [Test Independence](./test-independence.md)
- [Testing React Components](./testing-react-components.md)
- [Setup and Teardown](./setup-teardown.md)
