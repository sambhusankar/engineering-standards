# Waiting Strategies for Play Functions

Patterns for waiting on asynchronous operations in Storybook play functions.

## Why Wait?

Play functions often need to wait for:

- **DOM updates** after interactions
- **Animations/transitions** to complete
- **Portaled elements** to render
- **Async data** to load

## Strategy 1: waitFor with Assertion

Wait for an element to appear and assert it exists:

```javascript
import { waitFor, expect } from '@storybook/test';

await waitFor(async () => {
  const element = document.querySelector('[data-testid="item"]');
  await expect(element).toBeInTheDocument();
});
```

**When to use**: Waiting for portaled elements, dynamic content, or async operations.

**Example from codebase**:
```javascript
await waitFor(async () => {
  const calendar = document.querySelector('.MuiDateCalendar-root');
  await expect(calendar).toBeInTheDocument();
});
```

Source: src/components/DatePicker.stories.js:169-172

## Strategy 2: waitFor with Timeout

Set a custom timeout for slow operations:

```javascript
await waitFor(
  async () => {
    const element = canvas.getByText('Loaded');
    await expect(element).toBeVisible();
  },
  { timeout: 3000 }  // Wait up to 3 seconds
);
```

**When to use**: Operations that may take longer than default timeout (1s).

**Default timeout**: 1000ms (1 second)

## Strategy 3: Simple Delay

Wait for a fixed duration (animations, transitions):

```javascript
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
```

**When to use**:
- After clicking a button that triggers animation
- Opening dropdowns with CSS transitions
- Any operation with known duration

**Example from codebase**:
```javascript
const auto_open_dropdown = async ({ canvasElement }) => {
  const canvas = within(canvasElement);
  const trigger = canvas.getByRole('button', { name: /more options/i });
  await userEvent.click(trigger);
  await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
};
```

Source: src/components/FileDropzone.stories.js:67-76

## Choosing Wait Times

Match wait time to your component's behavior:

```javascript
// Short animations (200-300ms)
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));

// Medium animations (400-500ms)
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 500)));

// Long operations (1000ms+)
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 1000)));
```

**Tip**: Check your CSS transition duration or animation duration.

## Anti-Pattern: Arbitrary Waits

```javascript
// BAD: Random wait times without reasoning
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 742)));

// GOOD: Wait time matches transition duration
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
// Component has: transition: all 0.3s ease-in-out
```

## Combining Strategies

Often you'll use multiple strategies in sequence:

```javascript
export const MultiStepInteraction = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Step 1: Click and wait for animation
    const button = canvas.getByRole('button');
    await userEvent.click(button);
    await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));

    // Step 2: Wait for portaled element
    await waitFor(async () => {
      const menu = document.querySelector('[role="menu"]');
      await expect(menu).toBeInTheDocument();
    });

    // Step 3: Interact with menu item
    const menu_item = document.querySelector('[data-value="option-1"]');
    await userEvent.click(menu_item);

    // Step 4: Wait for selection to be reflected
    await waitFor(
      async () => {
        const selected = canvas.getByText('Option 1');
        await expect(selected).toBeVisible();
      },
      { timeout: 2000 }
    );
  },
};
```

## Common Pitfalls

### Pitfall 1: Not Waiting After Click

```javascript
// BAD: Click but don't wait for transition
await userEvent.click(trigger);
const menu = document.querySelector('[role="menu"]');
// Menu might not be rendered yet!

// GOOD: Wait for transition
await userEvent.click(trigger);
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
```

### Pitfall 2: Too Short Wait Time

```javascript
// BAD: Wait time shorter than animation
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 100)));
// But component has 300ms transition!

// GOOD: Match or exceed animation duration
await waitFor(() => new Promise((resolve) => setTimeout(resolve, 300)));
```

### Pitfall 3: Not Using waitFor for Assertions

```javascript
// BAD: Direct query without waiting
const element = document.querySelector('[data-testid="item"]');
await expect(element).toBeInTheDocument(); // Might fail!

// GOOD: Wrap in waitFor
await waitFor(async () => {
  const element = document.querySelector('[data-testid="item"]');
  await expect(element).toBeInTheDocument();
});
```

## Related Notes
- [Auto-Open Pattern](./play-functions-auto-open-pattern.md) - Using waits with auto-open
- [Portaled Components](./play-functions-portaled-components.md) - Waiting for portals
- [Play Functions](./play-functions.md) - Overview
