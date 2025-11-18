# Test Independence

Each test should be independent and not rely on other tests or shared state.

## The Problem

```javascript
// Bad - Tests depend on each other
describe('TodoList', () => {
  const list = new TodoList();  // Shared state

  it('should add todo', () => {
    list.add('Buy milk');
    expect(list.items).toHaveLength(1);  // Passes
  });

  it('should remove todo', () => {
    list.remove(0);
    expect(list.items).toHaveLength(0);  // Depends on previous test
  });
});
```

## The Solution

```javascript
// Good - Each test has its own setup
describe('TodoList', () => {
  it('should add todo', () => {
    const list = new TodoList();
    list.add('Buy milk');
    expect(list.items).toHaveLength(1);
  });

  it('should remove todo', () => {
    const list = new TodoList();
    list.add('Buy milk');
    list.remove(0);
    expect(list.items).toHaveLength(0);
  });
});
```

## Use beforeEach for Setup

When setup is repetitive, use `beforeEach`:

```javascript
describe('UserService', () => {
  let userService;
  let mockDb;

  beforeEach(() => {
    mockDb = createMockDatabase();
    userService = new UserService(mockDb);
  });

  it('should create user', async () => {
    const user = await userService.create({ name: 'John' });
    expect(user.name).toBe('John');
  });

  it('should find user by id', async () => {
    await userService.create({ id: '1', name: 'John' });
    const user = await userService.findById('1');
    expect(user.name).toBe('John');
  });
});
```

## Why Independence Matters

- **Reliability**: Tests don't fail due to execution order
- **Debugging**: Easier to isolate failures
- **Parallelization**: Can run tests in parallel
- **Maintainability**: Can modify/delete tests without affecting others

## Related Notes
- [Setup and Teardown](./setup-teardown.md)
- [AAA Pattern](./aaa-pattern.md)
- [Mock External Dependencies](./mock-external-dependencies.md)
