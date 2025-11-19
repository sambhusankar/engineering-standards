# Auto-Open Pattern for Play Functions

Common pattern for automatically opening dropdowns, dialogs, and collapsed UI elements in Storybook.

## Why Auto-Open?

Auto-opening collapsed UI serves two purposes:

1. **Visual review** - Immediately see the expanded state without manual clicks
2. **Screenshot/visual regression** - Capture expanded states automatically

## Basic Auto-Open Pattern

```javascript
import { within, userEvent, waitFor } from '@storybook/test';

const auto_open_dropdown = async ({ canvasElement }) => {
  const canvas = within(canvasElement);
  const trigger = canvas.getByRole('button', { name: /more options/i });
  await userEvent.click(trigger);
  await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
};

export const AfterOpeningDropdown = {
  args: { statement: mock_statement },
  play: auto_open_dropdown,
};
```

**Pattern breakdown**:
1. Find the trigger element
2. Click it with `userEvent.click()`
3. Wait for animation/transition to complete

## Reusable Play Functions

Extract common auto-open logic for reuse across stories:

```javascript
// Define once at top of file
const auto_open_calendar = async ({ canvasElement }) => {
  const canvas = within(canvasElement);
  const date_button = canvas.getByRole('button');
  await userEvent.click(date_button);
  await waitFor(() => new Promise((resolve) => setTimeout(resolve, 200)));
};

// Use in multiple stories
export const WithCalendarOpen = {
  args: { default_value: '' },
  play: auto_open_calendar,
};

export const AfterSelectingDate = {
  args: { default_value: '2024-01-15' },
  play: auto_open_calendar,
};
```

**Benefit**: Define once, reuse across all stories that need the same interaction.

## Real Example from Codebase

**FileDropzone auto-open**:

```javascript
const auto_open_dropdown = async ({ canvasElement }) => {
  const canvas = within(canvasElement);
  const trigger = canvas.getByRole('button', { name: /more options/i });
  await userEvent.click(trigger);
  await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
};

export const ReadyToUpload = {
  args: { /* ... */ },
  play: auto_open_dropdown,
};
```

Source: src/components/FileDropzone.stories.js:67-76

## Wait Times

Choose appropriate wait time based on animation duration:

```javascript
// Short animations (200-300ms)
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));

// Medium animations (400-500ms)
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 500)));

// Long animations or data fetching (1000ms+)
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 1000)));
```

**Tip**: Match the wait time to your component's transition duration to avoid flakiness.

## When to Use Auto-Open

**Use auto-open for**:
- Dropdowns/select menus
- Dialog/modal components
- Expandable sections
- Popovers/tooltips (with hover simulation)

**Skip auto-open for**:
- Data tables (focus on data states instead)
- Simple buttons (no collapsed state)
- Static components

## Related Notes
- [When to Use Play Functions](./play-functions-when-to-use.md) - Decision guidelines
- [Portaled Components](./play-functions-portaled-components.md) - Handling portals
- [Waiting Strategies](./play-functions-waiting-strategies.md) - Advanced waiting patterns
- [Play Functions](./play-functions.md) - Overview
