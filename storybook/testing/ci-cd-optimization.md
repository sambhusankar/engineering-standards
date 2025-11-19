# CI Performance Optimization

Strategies to speed up Storybook testing in CI/CD pipelines.

## Baseline Performance

**Without optimization**:
```
Install dependencies: 45s
Build Storybook: 60s
Run tests: 120s
Total: 225s (~4 minutes)
```

**With optimization**:
```
Install dependencies: 10s (cached)
Build Storybook: 15s (cached)
Run tests: 60s (4 workers)
Total: 85s (~1.5 minutes)
```

**Improvement**: 62% faster (2.6x speedup)

## Optimization Strategies

### 1. Cache Aggressively

See [CI Caching Strategies](/storybook/testing/ci-cd-caching.md) for details.

**Quick wins**:
- Dependencies: `cache: 'npm'` in setup-node action
- Playwright browsers: Cache `~/.cache/ms-playwright`
- Storybook builds: Cache `storybook-static` directory

**Impact**: Saves ~90 seconds per build

### 2. Increase Workers

More parallel workers = faster test execution:

```yaml
env:
  CI: true
  TEST_WORKERS: 4  # Default is 2
```

**Configuration** (`.storybook/test-runner.js`):
```javascript
module.exports = {
  maxWorkers: process.env.TEST_WORKERS || 2,
};
```

**Impact**: 40-50% faster tests with 4 workers vs. 2

**Trade-off**: More CPU usage. Adjust based on CI machine resources.

### 3. Split Test Suites

Run critical tests first, slow tests separately:

```yaml
strategy:
  matrix:
    suite: [critical, full]

steps:
  - name: Run Tests
    run: |
      if [ "${{ matrix.suite }}" == "critical" ]; then
        npx test-storybook --pattern="Button|Dialog|Form"
      else
        npx test-storybook
      fi
```

**Benefit**: Get fast feedback on core components, full suite runs in parallel.

### 4. Skip Unchanged Stories

Use coverage diff to test only changed components:

```yaml
- name: Get Changed Stories
  id: changed
  run: |
    changed_files=$(git diff --name-only origin/main | grep stories | sed 's/.stories.js//' | xargs basename -a | paste -sd "|" -)
    echo "pattern=$changed_files" >> $GITHUB_OUTPUT

- name: Run Tests on Changed Stories
  if: steps.changed.outputs.pattern != ''
  run: npx test-storybook --pattern="${{ steps.changed.outputs.pattern }}"

- name: Run Full Tests
  if: steps.changed.outputs.pattern == ''
  run: npm run storybook:test:ci
```

**Benefit**: Test only what changed in PRs, full suite on main branch.

## Failure Handling

### Retry Flaky Tests

```yaml
- name: Run Storybook Tests
  uses: nick-fields/retry@v3
  with:
    timeout_minutes: 10
    max_attempts: 2
    command: npm run storybook:test:ci
```

**Use case**: Handle flaky tests from timing issues without manual re-runs.

### Continue on Coverage Failure

During transition to 100% coverage:

```yaml
- name: Check Coverage
  continue-on-error: true
  run: npm run storybook:coverage
```

**Benefit**: Tests run but build doesn't fail on low coverage (temporary).

## Complete Optimized Workflow

```yaml
name: Storybook Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test-components:
    runs-on: ubuntu-latest
    timeout-minutes: 15

    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Cache Playwright Browsers
        uses: actions/cache@v4
        with:
          path: ~/.cache/ms-playwright
          key: ${{ runner.os }}-playwright-${{ hashFiles('**/package-lock.json') }}

      - name: Install Playwright Browsers
        run: npx playwright install --with-deps chromium

      - name: Run Storybook Tests
        run: npm run storybook:test:ci
        env:
          CI: true
          TEST_WORKERS: 4

      - name: Check Coverage
        run: npm run storybook:coverage
```

## Resource Allocation

### GitHub Actions Runners

**Standard runner** (ubuntu-latest):
- 2 CPU cores
- 7GB RAM
- Recommended workers: 2-4

**Larger runner** (ubuntu-latest-4-cores):
- 4 CPU cores
- 16GB RAM
- Recommended workers: 4-8

### Worker Calculation

Rule of thumb:
```
workers = cpu_cores * 1.5
```

For 2-core runner: 2-4 workers
For 4-core runner: 4-8 workers

## Monitoring Performance

Track build times over time:

```yaml
- name: Log Timing
  run: |
    echo "Build time: $(date +%s)" >> $GITHUB_STEP_SUMMARY
```

Compare runs in GitHub Actions UI to identify slowdowns.

## Related Notes

- [CI Caching Strategies](/storybook/testing/ci-cd-caching.md) - Detailed caching setup
- [GitHub Actions Integration](/storybook/testing/ci-cd-github-actions.md) - Basic workflow
- [CI Troubleshooting](/storybook/testing/ci-cd-troubleshooting.md) - Common issues
