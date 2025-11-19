# Running Tests (Development)

Commands for running Storybook tests during local development.

## Watch Mode (Recommended)

```bash
npm run storybook:test
```

**What it does**:
- Runs test-runner in **watch mode** with verbose output
- Re-runs tests when story files change
- Shows detailed test results in console
- Provides instant feedback

**Prerequisites**: Storybook dev server must be running:
```bash
# Terminal 1
npm run storybook:dev

# Terminal 2 (after Storybook loads)
npm run storybook:test
```

**When to use**: During active development for instant feedback on changes.

## Single Run (Quick Validation)

```bash
npx test-storybook --verbose
```

**What it does**:
- Runs all tests once
- Shows detailed output
- Exits with code 0 (success) or 1 (failure)

**When to use**: Quick validation before committing changes.

**Note**: Still requires Storybook dev server running in another terminal.

## Test Output Modes

### Verbose Mode

```bash
npx test-storybook --verbose
```

**Shows**:
- Each story being tested
- Play function execution details
- Console logs from stories
- Timing information
- Pass/fail status for each story

**Use case**: Understanding test execution and failures.

### Compact Mode

```bash
npx test-storybook
```

**Shows**:
- Summary of passed/failed tests
- Minimal output during execution
- Full error details on failures

**Use case**: Quick pass/fail check.

## Daily Development Workflow

### Setup

```bash
# Terminal 1: Keep Storybook running all day
npm run storybook:dev

# Terminal 2: Watch mode testing
npm run storybook:test
```

**Benefits**:
- Instant feedback as you write stories
- Auto-runs on file changes
- No manual re-running needed

### Development Loop

1. Edit component or story
2. Save file
3. Watch terminal - tests auto-run
4. Fix issues, repeat

## Before Committing

Two-step validation:

```bash
# 1. Run tests
npm run storybook:test:ci

# 2. Check coverage
npm run storybook:coverage
```

**Ensures**:
- All existing tests still pass
- Coverage hasn't dropped

See [Running Tests CI](./running-tests-ci.md) for CI mode details.

## Performance Tips

### Parallel Execution

Adjust workers in `.storybook/test-runner.js`:

```javascript
module.exports = {
  maxWorkers: process.env.CI ? 4 : 2,
};
```

More workers = faster tests (if you have CPU resources).

### Skip Slow Stories

Add `.ignore.` to filename:

```
Button.ignore.stories.js
```

Test runner will skip these stories.

**Use case**: Incomplete stories or stories being refactored.

### Focus on Specific Components

Instead of running all tests, use patterns:

```bash
# Test only Button component (faster)
npx test-storybook --pattern="Button"
```

See [Testing Pattern Matching](./testing-pattern-matching.md) for details.

## Exit Codes

- **0**: All tests passed
- **1**: One or more tests failed

**Use case**: Scripts can check exit codes to determine success/failure.

## Common Issues

### "Storybook not running on port 6006"

**Solution**: Start Storybook dev server first:
```bash
npm run storybook:dev
```

Wait for "Storybook X.X.X started" message before running tests.

### "Tests timeout"

**Cause**: Storybook dev server not fully started.

**Solution**: Wait longer, or check Storybook UI is accessible at http://localhost:6006.

### "Watch mode not detecting changes"

**Cause**: File watcher limits on Linux.

**Solution**:
```bash
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Related Notes

- [Running Tests CI](./running-tests-ci.md) - CI/CD pipeline commands
- [Running Tests Debugging](./running-tests-debugging.md) - Debug failing tests
- [Testing Development Workflow](./testing-development-workflow.md) - Daily usage patterns
- [Testing Pattern Matching](./testing-pattern-matching.md) - Testing specific components
