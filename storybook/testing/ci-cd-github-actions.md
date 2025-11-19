# GitHub Actions Integration

Basic GitHub Actions workflow setup for Storybook testing.

## Why Test in CI/CD?

**Benefits**:
- Catch regressions before merging to main
- Ensure all stories pass in production-like environment
- Enforce coverage requirements
- Prevent broken components from deploying

## Basic Workflow

Add Storybook testing step to your workflow:

**File**: `.github/workflows/test.yml`

```yaml
name: Test

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run Storybook Tests
        run: npm run storybook:test:ci

      - name: Upload test results
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: test-results/
          retention-days: 7
```

## With Coverage Enforcement

Add coverage check after tests:

```yaml
      - name: Run Storybook Tests
        run: npm run storybook:test:ci

      - name: Check Coverage
        run: npm run storybook:coverage
```

**Result**: Build fails if:
1. Any tests fail
2. Coverage < 100% (script exits with code 1)

## Parallel Testing

Run tests alongside unit tests:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        test-type: [unit, components]

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Run Tests
        run: |
          if [ "${{ matrix.test-type }}" == "unit" ]; then
            npm run test
          else
            npm run test:components
          fi
```

**Result**: Unit and component tests run in parallel jobs.

## CI Command

Use the dedicated CI command:

```bash
npm run storybook:test:ci
```

**What it does**:
1. Builds static Storybook
2. Serves on port 6006
3. Waits for server to be ready
4. Runs all tests
5. Kills processes and exits with appropriate code

**Why not use `npm run storybook:test`?**
- Requires Storybook already running (manual step)
- Watch mode doesn't exit (hangs CI)
- No built Storybook means dev dependencies needed

## Environment Variables

### Set Test Environment

```yaml
env:
  CI: true
  NODE_ENV: test
```

**Use case**: Adjust test-runner behavior in CI vs. local.

### Increase Workers

```yaml
env:
  CI: true
  TEST_WORKERS: 4
```

Then in `.storybook/test-runner.js`:

```javascript
module.exports = {
  maxWorkers: process.env.TEST_WORKERS || 2,
};
```

More workers = faster tests on CI servers with more CPU.

### Adjust Timeouts

```yaml
env:
  CI: true
  TEST_TIMEOUT: 60000
```

Then in `.storybook/test-runner.js`:

```javascript
module.exports = {
  timeout: {
    action: process.env.TEST_TIMEOUT || 10000,
    navigation: process.env.TEST_TIMEOUT * 3 || 30000,
  },
};
```

CI can be slower than local, so increase timeouts.

## Related Notes

- [CI Caching Strategies](./ci-cd-caching.md) - Speed up builds with caching
- [CI Artifacts and Reports](./ci-cd-artifacts.md) - Upload test results
- [CI Optimization](./ci-cd-optimization.md) - Performance tuning
- [CI Troubleshooting](./ci-cd-troubleshooting.md) - Common CI issues
