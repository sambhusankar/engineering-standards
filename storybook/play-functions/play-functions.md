# Storybook Play Functions

Play functions automate interactions with components in Storybook for testing and visual demonstration.

## What Are Play Functions?

Play functions are async functions that run after a story renders, allowing you to:

- **Auto-interact** with components (click, type, select)
- **Test behavior** with assertions
- **Demonstrate workflows** without manual interaction
- **Visual regression test** interactive states

## Basic Structure

```javascript
import { within, userEvent, expect } from '@storybook/test';

export const StoryName = {
  args: { /* component props */ },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Find element
    const button = canvas.getByRole('button', { name: /click me/i });

    // Interact
    await userEvent.click(button);

    // Assert (optional)
    await expect(button).toBeInTheDocument();
  },
};
```

## Required Imports

```javascript
import { within, userEvent, expect, waitFor } from '@storybook/test';
```

- `within` - Scope queries to story canvas
- `userEvent` - Simulate user interactions (click, type, etc.)
- `expect` - Make assertions
- `waitFor` - Wait for async changes

## Common Patterns

Play functions follow specific patterns depending on the use case:

### 1. When to Use Play Functions
Decide when play functions add value vs when to skip them.

**Read**: [When to Use Play Functions](./play-functions-when-to-use.md)

### 2. Auto-Open Pattern
Automatically open dropdowns, dialogs, and collapsed UI for visual review.

**Read**: [Auto-Open Pattern](./play-functions-auto-open-pattern.md)

### 3. Portaled Components
Handle components that render outside the story canvas (modals, dropdowns).

**Read**: [Portaled Components](./play-functions-portaled-components.md)

### 4. Waiting Strategies
Wait for animations, transitions, and async operations.

**Read**: [Waiting Strategies](./play-functions-waiting-strategies.md)

### 5. Assertions and Queries
Query elements and make assertions about component state.

**Read**: [Assertions and Queries](./play-functions-assertions.md)

## Quick Decision Guide

**Do I need a play function?**

```
Is component interactive (forms, dialogs, dropdowns)?
  ├─ Yes → Use play function
  │    └─ Read: When to Use Play Functions
  │
  └─ No (data display, static UI) → Skip play function
       └─ Focus stories on data states instead
```

**Do I need to auto-open collapsed UI?**

```
Does component have collapsed state (dropdown, dialog)?
  ├─ Yes → Use auto-open pattern
  │    └─ Read: Auto-Open Pattern
  │
  └─ No → Use basic play function or skip
```

**Is my component portaled?**

```
Does component render outside canvas (modal, dropdown menu)?
  ├─ Yes → Use document.querySelector for portaled elements
  │    └─ Read: Portaled Components
  │
  └─ No → Use canvas.getBy* queries
       └─ Read: Assertions and Queries
```

## Naming Convention

Use `snake_case` for play function variables:

```javascript
// GOOD
const auto_open_dropdown = async ({ canvasElement }) => { /* ... */ };
const verify_date_selected = async ({ canvasElement }) => { /* ... */ };

// BAD
const autoOpenDropdown = async ({ canvasElement }) => { /* ... */ };
const verifyDateSelected = async ({ canvasElement }) => { /* ... */ };
```

## Related Notes
- [When to Use Play Functions](./play-functions-when-to-use.md) - Decision guidelines
- [Auto-Open Pattern](./play-functions-auto-open-pattern.md) - Auto-opening collapsed UI
- [Portaled Components](./play-functions-portaled-components.md) - Handling portals
- [Waiting Strategies](./play-functions-waiting-strategies.md) - Async waiting patterns
- [Assertions and Queries](./play-functions-assertions.md) - Testing and queries
- [Storybook Interactions](../storybook-interactions.md) - Detailed interaction patterns
