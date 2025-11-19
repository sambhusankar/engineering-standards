# Test Runner Filtering

Filtering and ignoring specific stories during test execution.

## Ignore Pattern

Stories with `.ignore.` in filename are automatically skipped.

### Example

```
Button.ignore.stories.js  # Skipped
Button.stories.js         # Tested
```

### Implementation

In `.storybook/test-runner.js`:

```javascript
module.exports = {
  async preVisit(page, context) {
    if (context.title.includes('.ignore.')) {
      return 'skip';
    }
  },
};
```

**Return value**: `'skip'` tells test-runner to skip this story.

## Use Cases

### Incomplete Stories

Stories that are work-in-progress:

```bash
mv Button.stories.js Button.ignore.stories.js
```

Work on the story without breaking test suite.

**Remember**: Rename back when complete:
```bash
mv Button.ignore.stories.js Button.stories.js
```

### Flaky Stories

Stories with timing issues being fixed:

```bash
mv Dialog.stories.js Dialog.ignore.stories.js
```

**Better solution**: Fix the timing issues with `waitFor`.

See [Waiting Strategies](/storybook/play-functions/play-functions-waiting-strategies.md).

### Stories Not Ready for Automation

Manual-testing-only stories:

```
ManualTest.ignore.stories.js
```

**Note**: These stories still appear in Storybook UI, just not in automated tests.

## Conditional Skipping

Skip based on story metadata or content.

### Skip by Story Title

```javascript
async preVisit(page, context) {
  if (context.title.includes('WIP')) {
    return 'skip';
  }
}
```

**Example**: All stories with "WIP" in title are skipped.

### Skip by Story Name

```javascript
async preVisit(page, context) {
  if (context.name === 'ManualTestingOnly') {
    return 'skip';
  }
}
```

### Skip by Component Type

```javascript
async preVisit(page, context) {
  // Skip all Form stories temporarily
  if (context.title.startsWith('Forms/')) {
    return 'skip';
  }
}
```

### Skip in CI Only

```javascript
async preVisit(page, context) {
  // Skip slow stories in CI
  if (process.env.CI && context.title.includes('SlowStory')) {
    return 'skip';
  }
}
```

## Pattern-Based Filtering

Use test-runner's `--pattern` flag to filter at runtime:

### Test Specific Components

```bash
# Only test Button stories
npx test-storybook --pattern="Button"
```

### Exclude Components

```bash
# Test everything except ignored stories
npx test-storybook
```

See [Testing Pattern Matching](/storybook/testing/testing-pattern-matching.md) for advanced patterns.

## Filtering vs. Ignoring

### `.ignore.` Pattern

**Pros**:
- Permanent (survives git commits)
- Clear intent (filename shows it's ignored)
- Works automatically

**Cons**:
- Requires file rename
- Can be forgotten (stories stay ignored)

**Use when**: Story temporarily not ready.

### Conditional Skip in Hook

**Pros**:
- Flexible logic
- Environment-specific (skip in CI, run locally)
- No file renames needed

**Cons**:
- Less visible (hidden in config)
- Easy to forget to remove

**Use when**: Conditional behavior needed (CI vs. local).

### Pattern Matching

**Pros**:
- Runtime decision (no code changes)
- Fast (test only what you need)
- Flexible (regex support)

**Cons**:
- Manual (must remember pattern)
- Not enforced in CI (unless scripted)

**Use when**: Development testing, debugging specific components.

## Best Practices

### Don't Overuse Ignoring

**Problem**: Ignored stories accumulate, coverage drops.

**Solution**: Fix issues quickly, remove `.ignore.` pattern.

### Track Ignored Stories

```bash
# Find all ignored stories
find . -name "*.ignore.stories.*"
```

Regularly review and reduce this list.

### Prefer Fixing Over Ignoring

**Bad**:
```bash
mv FlakyTest.stories.js FlakyTest.ignore.stories.js
# Leave ignored indefinitely
```

**Good**:
```bash
mv FlakyTest.stories.js FlakyTest.ignore.stories.js
# Fix timing issue with waitFor
# Test passes
mv FlakyTest.ignore.stories.js FlakyTest.stories.js
```

### Use Descriptive Patterns

If using conditional skipping:

```javascript
async preVisit(page, context) {
  // Clear reason for skipping
  if (context.title.includes('[SKIP-CI]')) {
    console.log(`Skipping ${context.title} in CI`);
    return 'skip';
  }
}
```

## Related Notes

- [Test Runner Config](/storybook/testing/test-runner-config.md) - Configuration settings
- [Test Runner Hooks](/storybook/testing/test-runner-hooks.md) - Pre/post visit hooks
- [Testing Pattern Matching](/storybook/testing/testing-pattern-matching.md) - Runtime filtering
- [Coverage Requirements](/storybook/testing/coverage-requirements.md) - Coverage impact of ignoring
