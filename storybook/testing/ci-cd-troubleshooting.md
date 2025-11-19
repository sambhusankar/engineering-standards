# CI Troubleshooting

Common CI failures and solutions for Storybook testing.

## "Tests timeout waiting for Storybook"

**Symptom**: Test runner hangs waiting for port 6006.

**Cause**: Storybook build took too long or failed.

**Solution 1** - Add timeout to build:
```yaml
- name: Build Storybook with timeout
  run: timeout 5m npm run storybook:build
```

**Solution 2** - Check build logs:
```yaml
- name: Build Storybook
  run: npm run storybook:build --verbose
```

**Solution 3** - Increase wait-on timeout:
```bash
wait-on tcp:6006 --timeout 60000  # 60 seconds
```

## "Playwright browser not found"

**Symptom**: Error about missing Chromium installation.

**Cause**: Playwright browsers not installed in CI.

**Solution**:
```yaml
- name: Install Playwright Browsers
  run: npx playwright install --with-deps chromium
```

**Note**: Must run AFTER `npm ci` since it needs Playwright package installed first.

## "Tests flaky in CI but pass locally"

**Symptom**: Tests pass locally but randomly fail in CI.

**Cause**: CI is slower, causing timing issues.

**Solution 1** - Increase timeouts in test-runner.js:
```javascript
module.exports = {
  timeout: {
    action: process.env.CI ? 15000 : 10000,
    navigation: process.env.CI ? 60000 : 30000,
  },
};
```

**Solution 2** - Add explicit waits in play functions:
```javascript
await waitFor(() => expect(element).toBeVisible(), { timeout: 5000 });
```

**Solution 3** - Use retry action:
```yaml
- name: Run Storybook Tests
  uses: nick-fields/retry@v3
  with:
    max_attempts: 2
    command: npm run storybook:test:ci
```

See [Waiting Strategies](/storybook/play-functions/play-functions-waiting-strategies.md) for details.

## "Out of memory errors"

**Symptom**: Node/Playwright crashes with OOM error.

**Cause**: Too many parallel workers for available RAM.

**Solution 1** - Reduce workers:
```javascript
// .storybook/test-runner.js
maxWorkers: process.env.CI ? 2 : 4,
```

**Solution 2** - Increase Node memory:
```yaml
env:
  NODE_OPTIONS: --max-old-space-size=4096
```

**Solution 3** - Use smaller runner or request more RAM.

## "Module not found" errors

**Symptom**: Import errors for modules that exist locally.

**Cause**: Dependencies not installed or cache corruption.

**Solution 1** - Use `npm ci` not `npm install`:
```yaml
- name: Install dependencies
  run: npm ci  # NOT npm install
```

**Solution 2** - Clear npm cache:
```yaml
- name: Clear npm cache
  run: npm cache clean --force

- name: Install dependencies
  run: npm ci
```

**Solution 3** - Remove cache action temporarily to verify it's not cache corruption.

## "Permission denied" errors

**Symptom**: Cannot write to directories or execute scripts.

**Cause**: File permissions or ownership issues.

**Solution**:
```yaml
- name: Fix permissions
  run: chmod +x scripts/storybook-coverage.js

- name: Run coverage
  run: npm run storybook:coverage
```

## "Port 6006 already in use"

**Symptom**: Cannot start Storybook server.

**Cause**: Previous run didn't clean up properly.

**Solution** - Kill processes before starting:
```yaml
- name: Kill existing Storybook
  run: pkill -f "storybook" || true

- name: Run tests
  run: npm run storybook:test:ci
```

## "GitHub Actions artifacts upload failed"

**Symptom**: Artifacts don't upload after test failures.

**Cause**: Path doesn't exist or is empty.

**Solution** - Verify path exists:
```yaml
- name: Upload Test Results
  if: failure()
  run: |
    if [ -d "test-results" ]; then
      echo "Uploading test-results"
    else
      echo "test-results directory not found"
      exit 1
    fi

- name: Upload Artifacts
  if: failure()
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: test-results/
```

## "Coverage script fails silently"

**Symptom**: Coverage script doesn't exit with error code.

**Cause**: Script catches errors or doesn't throw properly.

**Solution** - Verify exit code:
```yaml
- name: Check Coverage
  run: |
    npm run storybook:coverage
    exit_code=$?
    if [ $exit_code -ne 0 ]; then
      echo "Coverage check failed with code $exit_code"
      exit $exit_code
    fi
```

## "Different behavior in CI vs local"

**Symptom**: Tests behave differently in CI than locally.

**Possible causes**:
1. **Environment variables** - CI has different env vars
2. **Viewport size** - CI uses different screen size
3. **Timezone** - CI uses UTC, local uses system timezone
4. **File system** - CI uses Linux, local might be macOS/Windows

**Solution** - Match CI environment locally:
```bash
# Run with CI flag
CI=true npm run storybook:test:ci

# Use same viewport
# (Set in test-runner.js)

# Use UTC timezone
TZ=UTC npm run storybook:test:ci
```

## Debugging Strategy

### 1. Enable verbose logging

```yaml
- name: Run Tests with Debug
  run: DEBUG=test-runner:* npm run storybook:test:ci
```

### 2. Run headed mode (if runner supports)

```yaml
- name: Run Tests Headed
  run: npx test-storybook --headed
```

### 3. Add diagnostic steps

```yaml
- name: Check Storybook build
  run: ls -la storybook-static

- name: Check dependencies
  run: npm list | head -20

- name: Check environment
  run: env | sort
```

### 4. Use tmate for SSH access

```yaml
- name: Setup tmate session
  if: failure()
  uses: mxschmitt/action-tmate@v3
```

Allows SSH into runner to debug interactively.

## Other CI Platforms

### GitLab CI Issues

**Cache not working**:
```yaml
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .cache/ms-playwright/
```

### CircleCI Issues

**Browser not found**:
```yaml
docker:
  - image: cimg/node:20.0-browsers  # Use -browsers variant
```

## Related Notes

- [GitHub Actions Integration](/storybook/testing/ci-cd-github-actions.md) - Basic setup
- [CI Optimization](/storybook/testing/ci-cd-optimization.md) - Performance tuning
- [Test Runner Config](/storybook/testing/test-runner-config.md) - Configuration settings
- [Running Tests Debugging](/storybook/testing/running-tests-debugging.md) - Local debugging
