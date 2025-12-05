# Test Runner Filtering

Skip stories during test execution using `.ignore.` pattern or conditional hooks.

## Ignore Pattern

Stories with `.ignore.` in filename are automatically skipped:

```
Button.ignore.stories.js  # Skipped
Button.stories.js         # Tested
```

### Implementation

```javascript
// .storybook/test-runner.js
module.exports = {
  async preVisit(page, context) {
    if (context.title.includes('.ignore.')) {
      return 'skip';
    }
  },
};
```

## Use Cases

```bash
# Work-in-progress story
mv Button.stories.js Button.ignore.stories.js
# Work on it, then rename back when complete
mv Button.ignore.stories.js Button.stories.js
```

## Conditional Skipping

```javascript
async preVisit(page, context) {
  // Skip by title pattern
  if (context.title.includes('WIP')) return 'skip';

  // Skip in CI only
  if (process.env.CI && context.title.includes('SlowStory')) return 'skip';
}
```

## Best Practices

- **Don't overuse**: Ignored stories accumulate, coverage drops
- **Track ignored**: `find . -name "*.ignore.stories.*"`
- **Fix over ignore**: Use `waitFor` to fix flaky tests instead of ignoring

## Related Notes
- [Testing Pattern Matching](/storybook/testing/testing-pattern-matching.md) - Runtime filtering with patterns
- [Test Runner Hooks](/storybook/testing/test-runner-hooks.md) - Pre/post visit hooks
