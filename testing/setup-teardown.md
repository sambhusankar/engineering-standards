# Setup and Teardown: beforeEach, afterEach

Use lifecycle hooks to avoid repetition in test setup and cleanup.

## Lifecycle Hooks

- `beforeAll()` - Runs once before all tests in block
- `beforeEach()` - Runs before each test
- `afterEach()` - Runs after each test
- `afterAll()` - Runs once after all tests in block

## Common Pattern

```javascript
describe('UserService', () => {
  let userService;
  let mockDb;

  beforeEach(() => {
    // Setup runs before each test
    mockDb = createMockDatabase();
    userService = new UserService(mockDb);
  });

  afterEach(() => {
    // Cleanup runs after each test
    mockDb.clear();
  });

  it('should create user', async () => {
    const user = await userService.create({ name: 'John' });
    expect(user.name).toBe('John');
  });

  it('should find user', async () => {
    await userService.create({ id: '1', name: 'John' });
    const user = await userService.findById('1');
    expect(user).toBeDefined();
  });
});
```

## Database Setup Example

```javascript
describe('User API Integration', () => {
  beforeAll(async () => {
    // One-time setup: database connection
    await db.connect();
    await db.migrate.latest();
  });

  beforeEach(async () => {
    // Clean database before each test
    await db.seed.run();
  });

  afterAll(async () => {
    // One-time cleanup: close connection
    await db.destroy();
  });

  it('should create user', async () => {
    const user = await createUser({ name: 'John' });
    expect(user.id).toBeDefined();
  });
});
```

## When to Use Each

**`beforeEach/afterEach`** - Use for test-specific setup/cleanup:
- Creating fresh instances
- Resetting mocks
- Clearing test data

**`beforeAll/afterAll`** - Use for expensive one-time operations:
- Database connections
- Server startup
- Loading fixtures

## Cleanup Best Practices

Always clean up resources:

```javascript
describe('FileProcessor', () => {
  let tempFile;

  beforeEach(() => {
    tempFile = createTempFile();
  });

  afterEach(() => {
    // Always clean up resources
    if (tempFile && fs.existsSync(tempFile)) {
      fs.unlinkSync(tempFile);
    }
  });

  it('should process file', () => {
    const result = processFile(tempFile);
    expect(result).toBeDefined();
  });
});
```

## Related Notes
- [Test Independence](./test-independence.md)
- [AAA Pattern](./aaa-pattern.md)
- [Integration Tests](./integration-tests.md)
