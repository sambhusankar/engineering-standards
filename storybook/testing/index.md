# Storybook Testing

Atomic notes on running automated tests against Storybook stories using the test-runner.

## Overview

Storybook stories serve as both documentation and automated tests. The `@storybook/test-runner` uses Playwright to visit each story, execute play functions, and validate component behavior.

This testing approach provides:
- **Component coverage tracking** - Every component should have stories
- **Automated regression testing** - Tests run in CI/CD pipelines
- **Visual and interaction validation** - Play functions test user workflows
- **Production scenario testing** - Stories represent real user states

## Test Runner Architecture

### Components

1. **Test Runner** (`@storybook/test-runner`)
   - Playwright-based automation
   - Visits each story URL
   - Executes play functions
   - Captures console errors
   - Generates test reports

2. **Coverage Script** (`scripts/storybook-coverage.js`)
   - Tracks which components have stories
   - Enforces 100% coverage requirement
   - Generates coverage reports and badges
   - Fails CI if coverage below threshold

3. **Mock System** (`.storybook/mocks/`)
   - Universal action mocking via Proxy
   - Static mock data for stories
   - Prevents database dependencies

## Atomic Notes

### Test Runner Setup
- [Test Runner Config](./test-runner-config.md) - Core configuration (browser, workers, timeouts)
- [Test Runner Hooks](./test-runner-hooks.md) - Pre/post visit hooks for custom logic
- [Test Runner Filtering](./test-runner-filtering.md) - Ignoring and filtering stories

### Running Tests
- [Running Tests Development](./running-tests-development.md) - Watch mode, quick validation
- [Running Tests CI](./running-tests-ci.md) - CI/CD pipeline commands
- [Running Tests Debugging](./running-tests-debugging.md) - Debug mode, headed, reports

### Testing Specific Components
- [Testing Pattern Matching](./testing-pattern-matching.md) - Pattern syntax for filtering tests
- [Testing Development Workflow](./testing-development-workflow.md) - Daily development patterns
- [Testing Debugging Individual](./testing-debugging-individual.md) - Debug specific failing tests

### Coverage Tracking
- [Coverage Script](./coverage-script.md) - How the script works
- [Coverage Requirements](./coverage-requirements.md) - What counts as component/covered
- [Coverage Reports](./coverage-reports.md) - Understanding reports and badges
- [Coverage Improving](./coverage-improving.md) - Strategies to reach 100%

### CI/CD Integration
- [CI GitHub Actions](./ci-cd-github-actions.md) - GitHub Actions workflow setup
- [CI Caching](./ci-cd-caching.md) - Speed up builds with caching
- [CI Artifacts](./ci-cd-artifacts.md) - Uploading test results and reports
- [CI Optimization](./ci-cd-optimization.md) - Performance tuning for CI
- [CI Troubleshooting](./ci-cd-troubleshooting.md) - Common CI failures and solutions

## Quick Commands

```bash
# Development (watch mode with verbose output)
npm run storybook:test

# CI mode (build + serve + test)
npm run storybook:test:ci

# Coverage report
npm run storybook:coverage

# Test specific story pattern
npx test-storybook --pattern="Button"
```

## Test Flow

1. **Story Creation** - Write stories following user journey pattern
2. **Play Functions** - Add automated interactions (if appropriate)
3. **Local Testing** - Run `npm run storybook:test` during development
4. **Coverage Check** - Run `npm run storybook:coverage` to ensure component covered
5. **CI Validation** - Tests run automatically on push to ensure no regressions

## Coverage Requirements

- **Target**: 100% component coverage
- **Tracked**: All `.jsx`/`.tsx` files in `/src`
- **Excluded**: Tests, stories, Next.js special files (page.jsx, layout.jsx, etc.)
- **Enforcement**: Coverage script exits with error if below 100% (for future CI enforcement)

## Getting Started

### For First-Time Setup
1. Read: [Test Runner Config](./test-runner-config.md) - Understand the configuration
2. Read: [Running Tests Development](./running-tests-development.md) - Learn the commands

### For Daily Development
1. Write stories following [User Journey Pattern](../storybook-user-journey-pattern.md)
2. Add play functions if component is interactive ([When to Use](../play-functions/play-functions-when-to-use.md))
3. Run `npm run storybook:test` to validate
4. Check coverage with `npm run storybook:coverage`

### For CI/CD Integration
1. Read: [CI GitHub Actions](./ci-cd-github-actions.md)
2. Add test step to GitHub Actions workflow
3. Configure coverage enforcement

## Common Use Cases

### "How do I run tests locally?"
→ [Running Tests Development](./running-tests-development.md)

### "How do I test just one component?"
→ [Testing Pattern Matching](./testing-pattern-matching.md)

### "How do I check coverage?"
→ [Coverage Reports](./coverage-reports.md)

### "How do I add tests to CI?"
→ [CI GitHub Actions](./ci-cd-github-actions.md)

### "What is the test-runner doing?"
→ [Test Runner Config](./test-runner-config.md)

### "Tests are failing, how do I debug?"
→ [Running Tests Debugging](./running-tests-debugging.md)

### "How do I improve coverage?"
→ [Coverage Improving](./coverage-improving.md)

## Related Topics

- [Play Functions](../play-functions/play-functions.md) - Writing automated interactions
- [Storybook Mock Data](../storybook-mock-data.md) - Mocking server actions and data
- [User Journey Pattern](../storybook-user-journey-pattern.md) - Story philosophy
