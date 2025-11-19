# Testing Debugging Individual

Debugging strategies for failing story tests.

## Verbose Output

Show detailed logs during test execution:

```bash
npx test-storybook --pattern="Login" --verbose
```

**Shows**:
- Each story being tested
- Play function execution steps
- Console logs from stories
- Timing information
- Detailed error messages

**Use case**: Understanding why a test fails.

## Headed Mode

Run tests in visible browser window:

```bash
npx test-storybook --pattern="Dialog" --headed
```

**Shows**:
- Browser window opening
- Component rendering
- Play function interactions
- Visual state changes

**Use case**: Timing issues, visual debugging, understanding interaction flow.

**Note**: Tests run slower in headed mode.

## Debug Logs

Enable test-runner internal logging:

```bash
DEBUG=test-runner:* npx test-storybook --pattern="Form"
```

**Shows**:
- Test runner lifecycle
- Page navigation events
- Hook execution
- Internal errors

**Use case**: Debugging test-runner configuration issues.

## View Test Reports

After tests complete, view interactive HTML report:

```bash
# After test fails
npx playwright show-report test-results
```

**Contains**:
- Screenshots of failures
- Console logs
- Timing traces
- Error stack traces

**Use case**: Post-mortem analysis of failed tests.

## Combining Debug Flags

Stack multiple debug options:

```bash
# Verbose + Headed + Debug logs
DEBUG=test-runner:* npx test-storybook \
  --pattern="Component" \
  --verbose \
  --headed
```

**Maximum visibility** into test execution.

## Common Debugging Scenarios

### "Test passes locally but fails in CI"

**Diagnosis**:
```bash
# Run with CI environment variables
CI=true npx test-storybook --pattern="Component"
```

**Possible causes**:
1. Timing differences (CI slower)
2. Viewport differences
3. Environment variables

See [CI Troubleshooting](/storybook/testing/ci-cd-troubleshooting.md) for solutions.

### "Play function doesn't complete"

**Diagnosis**:
```bash
# Run headed to watch execution
npx test-storybook --pattern="Component" --headed
```

**Possible causes**:
1. Element not found
2. Waiting for element that never appears
3. Timeout too short

**Fix**: Add explicit waits, increase timeouts.

See [Waiting Strategies](/storybook/play-functions/play-functions-waiting-strategies.md).

### "Element not found errors"

**Diagnosis**:
```bash
# Check console logs in verbose mode
npx test-storybook --pattern="Component" --verbose
```

**Common causes**:
1. Element not rendered yet (timing)
2. Incorrect selector
3. Element in portal (not in canvasElement)

**Fix**:
```javascript
// Use waitFor
await waitFor(() => expect(element).toBeVisible());

// Or check document for portaled elements
const dialog = document.querySelector('[role="dialog"]');
```

See [Portaled Components](/storybook/play-functions/play-functions-portaled-components.md).

### "Console errors fail test"

**Diagnosis**:
```bash
# See which console errors occur
npx test-storybook --pattern="Component" --verbose
```

**Cause**: Post-visit hook checks `window.__errors`.

**Fix**: Remove console.error calls or handle errors properly.

## Screenshot Debugging

If test-runner configured to capture screenshots:

### View Screenshots

```bash
# After test run
ls test-results/screenshots/

# Or view in HTML report
npx playwright show-report test-results
```

**Use case**: Visual regression, understanding UI state at failure.

### Enable Screenshot Capture

In `.storybook/test-runner.js`:

```javascript
async postVisit(page, context) {
  await page.screenshot({
    path: `test-results/screenshots/${context.id}.png`,
    fullPage: true,
  });
}
```

## Isolating Failures

### Test Single File

```bash
npx test-storybook --pattern="ExactFileName.stories"
```

Narrows scope to one file.

### Temporarily Ignore Other Files

```bash
# Rename other files to .ignore.
mv Button.stories.js Button.ignore.stories.js
mv Card.stories.js Card.ignore.stories.js

# Run all tests (only non-ignored run)
npx test-storybook

# Rename back after debugging
mv Button.ignore.stories.js Button.stories.js
mv Card.ignore.stories.js Card.stories.js
```

## Comparing Local vs. CI

Run tests with CI settings locally:

```bash
# Match CI environment
CI=true TEST_WORKERS=4 TEST_TIMEOUT=60000 \
  npx test-storybook --pattern="Component"
```

**Helps identify** CI-specific issues.

## Related Notes

- [Testing Pattern Matching](/storybook/testing/testing-pattern-matching.md) - Focusing on specific tests
- [Testing Development Workflow](/storybook/testing/testing-development-workflow.md) - Daily testing patterns
- [CI Troubleshooting](/storybook/testing/ci-cd-troubleshooting.md) - CI-specific issues
- [Play Functions Waiting Strategies](/storybook/play-functions/play-functions-waiting-strategies.md) - Timing fixes
- [Play Functions Portaled Components](/storybook/play-functions/play-functions-portaled-components.md) - Finding portaled elements
