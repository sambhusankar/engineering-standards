# Portaled Components in Play Functions

How to interact with components that render outside the story canvas (modals, dropdowns, tooltips).

## The Portal Problem

Many UI libraries (Material-UI, Radix, etc.) render certain components **outside the story canvas** using React portals:

- Modals/dialogs
- Dropdown menus
- Tooltips
- Select options
- Popovers

**Challenge**: These elements aren't within `canvasElement`, so `canvas.getBy*()` queries won't find them.

## Solution: Use `document` for Portaled Elements

```javascript
import { within, userEvent, expect, waitFor } from '@storybook/test';

export const WithOpenDropdown = {
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // 1. Find and click trigger (INSIDE canvas)
    const trigger = canvas.getByRole('button');
    await userEvent.click(trigger);

    // 2. Find portaled element (OUTSIDE canvas - use document)
    await waitFor(async () => {
      const menu_item = document.querySelector('[data-testid="menu-item"]');
      await expect(menu_item).toBeInTheDocument();
    });

    // 3. Interact with portaled element
    const menu_item = document.querySelector('[data-testid="menu-item"]');
    await userEvent.click(menu_item);
  },
};
```

## Key Difference: canvas vs document

| Query Location | Use | Example |
|---------------|-----|---------|
| `canvas.getBy*()` | Elements **inside** story canvas | Trigger buttons, form inputs, component content |
| `document.querySelector()` | Elements **outside** story canvas | Portaled dropdowns, modals, tooltips |

## Real Example: MultiSelect with Portaled Options

```javascript
export const AfterSelectingOption = {
  args: { options: mock_options },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Click trigger (inside canvas)
    const trigger = canvas.getByRole('combobox');
    await userEvent.click(trigger);

    // Select option (portaled outside canvas)
    await waitFor(async () => {
      const option = document.querySelector('[data-value="option-1"]');
      await expect(option).toBeInTheDocument();
      await userEvent.click(option);
    });
  },
};
```

Source: src/components/MultiSelect.stories.js:162-180

## Real Example: DatePicker with Portaled Calendar

```javascript
export const AfterSelectingDate = {
  args: { default_value: '2024-01-15' },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Step 1: Open calendar (trigger is in canvas)
    const date_button = canvas.getByRole('button');
    await userEvent.click(date_button);

    // Step 2: Wait for calendar (portaled outside canvas)
    await waitFor(async () => {
      const calendar = document.querySelector('.MuiDateCalendar-root');
      await expect(calendar).toBeInTheDocument();
    });

    // Step 3: Verify selected date (in portaled calendar)
    const selected_date = document.querySelector('[aria-current="date"]');
    await expect(selected_date).toHaveTextContent('15');
  },
};
```

Source: src/components/DatePicker.stories.js:162-180

## Best Practice: Wait for Portal

Always wrap portal queries in `waitFor()`:

```javascript
// GOOD: Wait for portal to render
await waitFor(async () => {
  const dialog = document.querySelector('[role="dialog"]');
  await expect(dialog).toBeInTheDocument();
});

// BAD: Direct query might fail if portal hasn't rendered yet
const dialog = document.querySelector('[role="dialog"]');
await expect(dialog).toBeInTheDocument(); // May fail due to timing
```

## Common Portal Selectors

```javascript
// Material-UI Modals
document.querySelector('.MuiModal-root')

// Material-UI Popper (dropdowns, tooltips)
document.querySelector('.MuiPopper-root')

// Material-UI Select options
document.querySelector('[data-value="option-id"]')

// Material-UI DateCalendar
document.querySelector('.MuiDateCalendar-root')

// Generic ARIA dialogs
document.querySelector('[role="dialog"]')

// Generic ARIA menus
document.querySelector('[role="menu"]')
```

## Pattern Summary

```javascript
const interact_with_portaled_ui = async ({ canvasElement }) => {
  const canvas = within(canvasElement);

  // STEP 1: Find trigger in canvas
  const trigger = canvas.getByRole('button');

  // STEP 2: Click trigger
  await userEvent.click(trigger);

  // STEP 3: Wait for portaled element
  await waitFor(async () => {
    const portaled = document.querySelector('[selector]');
    await expect(portaled).toBeInTheDocument();
  });

  // STEP 4: Interact with portaled element
  const portaled = document.querySelector('[selector]');
  await userEvent.click(portaled);
};
```

## Related Notes
- [Auto-Open Pattern](./play-functions-auto-open-pattern.md) - Auto-opening collapsed UI
- [Waiting Strategies](./play-functions-waiting-strategies.md) - Advanced waiting patterns
- [Play Functions](./play-functions.md) - Overview
