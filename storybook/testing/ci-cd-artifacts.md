# CI Artifacts and Reports

Uploading test results and coverage reports from CI builds.

## Why Upload Artifacts?

**Benefits**:
- Debug failed tests with screenshots and logs
- Track coverage over time
- Share results with team via PR comments
- Historical data for analysis

## Upload Test Results on Failure

Save test results when tests fail:

```yaml
- name: Upload Test Results
  if: failure()
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: test-results/
    retention-days: 7
```

**Contains**:
- HTML report with screenshots
- JSON results
- Error logs

**Access**: Download from GitHub Actions UI under "Artifacts" section.

**Retention**: 7 days (customize with `retention-days`).

## Upload Coverage Report

Always upload coverage, even on success:

```yaml
- name: Generate Coverage Report
  run: npm run storybook:coverage

- name: Upload Coverage Report
  uses: actions/upload-artifact@v4
  with:
    name: coverage-report
    path: dev_docs/storybook/
    retention-days: 30
```

**Contains**:
- Coverage markdown report
- Coverage badge SVG

**Retention**: 30 days to track trends.

## Comment Coverage on PR

Post coverage report as PR comment:

```yaml
- name: Comment Coverage on PR
  if: github.event_name == 'pull_request'
  uses: actions/github-script@v7
  with:
    script: |
      const fs = require('fs');
      const coverage = fs.readFileSync('dev_docs/storybook/coverage-report.md', 'utf8');
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: coverage
      });
```

**Result**: Coverage report appears automatically in PR conversation.

**Benefit**: Reviewers see coverage changes without downloading artifacts.

## Viewing Test Results

After downloading artifacts:

```bash
# Extract artifacts.zip
unzip test-results.zip

# Open HTML report
npx playwright show-report test-results
```

Opens interactive report showing:
- Failed tests with screenshots
- Console logs
- Timing traces
- Error stack traces

## Conditional Uploads

Upload only on specific conditions:

```yaml
# Upload only on failure
- name: Upload Test Results
  if: failure()
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: test-results/

# Upload only on main branch
- name: Upload Coverage
  if: github.ref == 'refs/heads/main'
  uses: actions/upload-artifact@v4
  with:
    name: coverage-report
    path: dev_docs/storybook/

# Upload only on pull requests
- name: Upload Results
  if: github.event_name == 'pull_request'
  uses: actions/upload-artifact@v4
  with:
    name: pr-test-results
    path: test-results/
```

## Complete Example

```yaml
- name: Run Storybook Tests
  run: npm run storybook:test:ci

- name: Check Coverage
  run: npm run storybook:coverage

- name: Upload Test Results
  if: failure()
  uses: actions/upload-artifact@v4
  with:
    name: test-results
    path: test-results/
    retention-days: 7

- name: Upload Coverage Report
  uses: actions/upload-artifact@v4
  with:
    name: coverage-report
    path: dev_docs/storybook/
    retention-days: 30

- name: Comment Coverage on PR
  if: github.event_name == 'pull_request'
  uses: actions/github-script@v7
  with:
    script: |
      const fs = require('fs');
      const coverage = fs.readFileSync('dev_docs/storybook/coverage-report.md', 'utf8');
      github.rest.issues.createComment({
        issue_number: context.issue.number,
        owner: context.repo.owner,
        repo: context.repo.repo,
        body: coverage
      });
```

## Related Notes

- [GitHub Actions Integration](/storybook/testing/ci-cd-github-actions.md) - Basic workflow
- [Coverage Requirements](/storybook/testing/coverage-requirements.md) - Understanding coverage
- [CI Optimization](/storybook/testing/ci-cd-optimization.md) - Performance tuning
