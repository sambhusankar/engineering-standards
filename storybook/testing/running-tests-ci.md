# Running Tests (CI)

Commands for running Storybook tests in CI/CD pipelines.

## Full CI Pipeline

```bash
npm run storybook:test:ci
```

**What it does**:
1. Builds static Storybook (`npm run storybook:build`)
2. Serves built Storybook on port 6006 (`npm run storybook:serve`)
3. Waits for port 6006 to be ready (`wait-on tcp:6006`)
4. Runs all tests against built Storybook
5. Kills all processes when tests complete

**Implementation** (package.json):
```json
{
  "storybook:test:ci": "concurrently -k -s first -n \"SB,TEST\" -c \"magenta,blue\" \"npm run storybook:build && npm run storybook:serve\" \"wait-on tcp:6006 && npm run storybook:test\""
}
```

**Flags**:
- `-k` Kill all processes when one exits
- `-s first` Return exit code of first process to complete
- `-n "SB,TEST"` Names for logging output
- `-c "magenta,blue"` Colors for output

**When to use**: In GitHub Actions or other CI/CD pipelines.

## Component Test Alias

```bash
npm run test:components
```

**What it does**: Same as `npm run storybook:test:ci` (convenience alias).

**When to use**: In CI pipelines alongside unit tests.

**Example** (GitHub Actions):
```yaml
- name: Run Component Tests
  run: npm run test:components
```

## Why CI Mode Differs from Dev Mode

### Dev Mode (`npm run storybook:test`)

**Requires**: Storybook dev server already running
**Mode**: Watch mode (doesn't exit)
**Speed**: Fast (no build step)
**Use case**: Local development

**Problems in CI**:
- Watch mode doesn't exit (hangs CI)
- Requires manual Storybook start
- Needs dev dependencies

### CI Mode (`npm run storybook:test:ci`)

**Requires**: Nothing (self-contained)
**Mode**: Single run (exits with code)
**Speed**: Slower (includes build)
**Use case**: Automated CI/CD

**Benefits**:
- No manual steps
- Exits with proper code
- Tests production build

## Before Committing

Run CI mode locally to verify:

```bash
npm run storybook:test:ci
```

**Ensures** tests will pass in CI.

## Exit Codes

- **0**: All tests passed
- **1**: One or more tests failed, or build failed

**Use case**: CI pipelines fail build on non-zero exit code.

## Common CI Workflows

### GitHub Actions

```yaml
- name: Run Storybook Tests
  run: npm run storybook:test:ci

- name: Check Coverage
  run: npm run storybook:coverage
```

See [CI GitHub Actions](./ci-cd-github-actions.md) for complete workflow.

### With Coverage Enforcement

```yaml
- name: Run Tests and Coverage
  run: |
    npm run storybook:test:ci
    npm run storybook:coverage
```

Build fails if:
1. Any tests fail
2. Coverage < 100%

## Troubleshooting

### "Tests timeout waiting for port"

**Solution**: Increase `wait-on` timeout in script:
```bash
wait-on tcp:6006 --timeout 60000  # 60 seconds
```

### "Build fails but tests would pass"

**Cause**: Storybook build errors (missing dependencies, config issues).

**Solution**: Run build separately to see errors:
```bash
npm run storybook:build
```

### "Tests pass locally but fail in CI"

**Possible causes**:
1. Timing differences (CI is slower)
2. Viewport differences
3. Missing environment variables
4. Different Node/browser versions

See [CI Troubleshooting](./ci-cd-troubleshooting.md) for solutions.

## Performance Optimization

### Cache Storybook Build

In CI, cache the `storybook-static` directory:

```yaml
- name: Cache Storybook Build
  uses: actions/cache@v4
  with:
    path: storybook-static
    key: storybook-${{ hashFiles('src/**/*.stories.*') }}
```

See [CI Caching](./ci-cd-caching.md) for details.

### Increase Workers

Set environment variable for more parallel execution:

```yaml
env:
  TEST_WORKERS: 4
```

Then in `.storybook/test-runner.js`:
```javascript
maxWorkers: process.env.TEST_WORKERS || 2
```

See [CI Optimization](./ci-cd-optimization.md) for more strategies.

## Related Notes

- [Running Tests Development](./running-tests-development.md) - Local development commands
- [Running Tests Debugging](./running-tests-debugging.md) - Debug failures
- [CI GitHub Actions](./ci-cd-github-actions.md) - GitHub Actions integration
- [CI Optimization](./ci-cd-optimization.md) - Performance tuning
- [CI Troubleshooting](./ci-cd-troubleshooting.md) - Common CI issues
