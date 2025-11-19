# Running Tests (Debugging)

Commands and strategies for debugging failing Storybook tests.

## View HTML Report

After tests complete, view interactive report:

```bash
npx playwright show-report test-results
```

**Opens**: Interactive HTML report in browser

**Contains**:
- Screenshots of failures
- Console logs
- Timing traces
- Error stack traces

**Use case**: Post-mortem analysis of test failures.

## Enable Debug Mode

Show test-runner internal logs:

```bash
DEBUG=test-runner:* npx test-storybook
```

**Shows**:
- Test runner lifecycle
- Page navigation events
- Hook execution
- Internal errors

**Use case**: Understanding test-runner behavior and configuration issues.

## Run Headed (Show Browser)

Open visible browser window during tests:

```bash
npx test-storybook --headed
```

**Shows**:
- Browser window opening
- Component rendering in real-time
- Play function interactions
- Visual state changes

**Use case**: Debugging interaction timing issues, visual problems.

**Note**: Tests run slower in headed mode.

## Test Specific Components

Debug only failing component instead of full suite:

```bash
npx test-storybook --pattern="FailingComponent"
```

See [Testing Pattern Matching](./testing-pattern-matching.md) for pattern syntax.

## Combine Debug Flags

Stack multiple debugging options:

```bash
# Verbose + Headed + Debug logs
DEBUG=test-runner:* npx test-storybook \
  --pattern="Component" \
  --verbose \
  --headed
```

**Maximum visibility** into test execution.

## Common Troubleshooting Scenarios

### "Tests pass locally but fail in CI"

**Diagnosis**:
```bash
# Run with CI environment variables
CI=true npx test-storybook
```

**Possible causes**:
1. **Timing differences** - CI is slower
   - Increase timeouts in test-runner.js
2. **Viewport differences** - CI uses different screen size
   - Check viewport settings in test-runner.js
3. **Missing environment variables** - CI has different env vars
   - Ensure CI has same env vars as local

See [CI Troubleshooting](./ci-cd-troubleshooting.md) for detailed solutions.

### "Flaky tests that randomly fail"

**Solutions**:

1. **Add explicit waits** in play functions:
   ```javascript
   await waitFor(() => expect(element).toBeVisible(), { timeout: 5000 });
   ```

2. **Use longer timeouts**:
   ```javascript
   // .storybook/test-runner.js
   timeout: {
     action: 15000,  // Increased from 10000
     navigation: 60000,  // Increased from 30000
   }
   ```

3. **Check for race conditions** in async operations

4. **Increase retries** in test-runner.js:
   ```javascript
   retries: 2  // Retry twice on failure
   ```

See [Waiting Strategies](../play-functions/play-functions-waiting-strategies.md) for details.

### "Element not found" errors

**Diagnosis**:
```bash
# Run headed to see what's happening
npx test-storybook --pattern="Component" --headed
```

**Common causes**:
1. Element not rendered yet (timing issue)
2. Incorrect selector
3. Element in portal (not in canvasElement)

**Solutions**:
- Use `waitFor` for timing
- Check selector is correct
- For portaled elements, use `document.querySelector`

See [Portaled Components](../play-functions/play-functions-portaled-components.md).

### "Tests timeout"

**Diagnosis**:
```bash
# Check if Storybook is actually running
curl http://localhost:6006
```

**Solutions**:
1. **Storybook not running**:
   ```bash
   npm run storybook:dev
   ```

2. **Build too slow**:
   ```bash
   wait-on tcp:6006 --timeout 60000  # Increase timeout
   ```

3. **Story takes long to render**:
   ```javascript
   // Increase navigation timeout
   timeout: { navigation: 60000 }
   ```

## Debugging Strategies

### Step 1: Isolate the Failure

```bash
# Test only the failing component
npx test-storybook --pattern="FailingComponent"
```

### Step 2: Add Visibility

```bash
# Run in verbose + headed mode
npx test-storybook --pattern="FailingComponent" --verbose --headed
```

### Step 3: Check Logs

```bash
# Enable debug logging
DEBUG=test-runner:* npx test-storybook --pattern="FailingComponent"
```

### Step 4: View Reports

```bash
# After failure, check HTML report
npx playwright show-report test-results
```

### Step 5: Fix and Verify

```bash
# Fix the issue, then test again
npx test-storybook --pattern="FailingComponent"

# Once passing, run full suite
npm run storybook:test:ci
```

## Debugging in CI

### Enable Artifacts Upload

In GitHub Actions:
```yaml
- name: Upload Test Results
  if: failure()
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: test-results/
```

**Download artifacts** from GitHub UI to view reports locally.

See [CI Artifacts](./ci-cd-artifacts.md) for details.

### Run CI Command Locally

```bash
# Simulate CI environment
CI=true npm run storybook:test:ci
```

**Helps identify** CI-specific issues.

## Related Notes

- [Running Tests Development](./running-tests-development.md) - Local development
- [Running Tests CI](./running-tests-ci.md) - CI/CD commands
- [Testing Debugging Individual](./testing-debugging-individual.md) - Individual story debugging
- [CI Troubleshooting](./ci-cd-troubleshooting.md) - CI-specific issues
- [Waiting Strategies](../play-functions/play-functions-waiting-strategies.md) - Timing issues
