# Play Function Assertions and Queries

Common assertions and query patterns for testing components in Storybook play functions.

## Query Patterns

### Queries Within Canvas

Use `canvas.getBy*()` for elements inside the story:

```javascript
import { within } from '@storybook/test';

const canvas = within(canvasElement);

// By role (preferred - most accessible)
const button = canvas.getByRole('button', { name: /click me/i });
const input = canvas.getByRole('textbox');
const checkbox = canvas.getByRole('checkbox', { name: /accept/i });

// By text
const element = canvas.getByText('Hello world');
const partial = canvas.getByText(/hello/i);  // Case-insensitive regex

// By test ID
const element = canvas.getByTestId('submit-button');
```

### Queries for Portaled Elements

Use `document.querySelector()` for portaled elements (modals, dropdowns):

```javascript
// Material-UI specific
const calendar = document.querySelector('.MuiDateCalendar-root');
const menu_item = document.querySelector('[data-value="option-1"]');
const modal = document.querySelector('.MuiModal-root');

// Generic ARIA
const dialog = document.querySelector('[role="dialog"]');
const menu = document.querySelector('[role="menu"]');
```

See: [Portaled Components](/storybook/play-functions/play-functions-portaled-components.md) for details.

## Common Assertions

### Presence Assertions

```javascript
import { expect } from '@storybook/test';

// Element exists in DOM
await expect(element).toBeInTheDocument();

// Element is visible (not hidden)
await expect(element).toBeVisible();
```

### Text Content Assertions

```javascript
// Exact text match
await expect(element).toHaveTextContent('exact text');

// Partial match
await expect(element).toHaveTextContent(/partial/i);

// Specific content
await expect(heading).toHaveTextContent('Dashboard');
```

### Attribute Assertions

```javascript
// Check attribute value
await expect(input).toHaveAttribute('placeholder', 'Enter date');
await expect(input).toHaveAttribute('type', 'text');

// Check input value
await expect(input).toHaveValue('2024-01-15');
await expect(input).toHaveValue('');

// Check class
await expect(element).toHaveClass('active');
```

### State Assertions

```javascript
// Element state
await expect(button).toBeEnabled();
await expect(button).toBeDisabled();
await expect(checkbox).toBeChecked();
await expect(input).toHaveFocus();
```

### Style Assertions

```javascript
// Check className contains string
await expect(input.className).toContain('bg-primary');
await expect(input.className).toContain('bg-primary/5');
```

## Real Examples from Codebase

### Example 1: Simple Verification

```javascript
export const BeforeSelectingDate = {
  args: { default_value: '' },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);
    const date_button = canvas.getByRole('button', { name: /select a date/i });
    await expect(date_button).toBeInTheDocument();
  },
};
```

Source: src/components/DatePicker.stories.js:79-92

### Example 2: Multi-Step with Portals

```javascript
export const AfterSelectingDate = {
  args: { default_value: '2024-01-15' },
  play: async ({ canvasElement }) => {
    const canvas = within(canvasElement);

    // Open calendar
    const date_button = canvas.getByRole('button');
    await userEvent.click(date_button);

    // Wait for portaled calendar
    await waitFor(async () => {
      const calendar = document.querySelector('.MuiDateCalendar-root');
      await expect(calendar).toBeInTheDocument();
    });

    // Verify selected date
    const selected_date = document.querySelector('[aria-current="date"]');
    await expect(selected_date).toHaveTextContent('15');
  },
};
```

Source: src/components/DatePicker.stories.js:162-180

## Query Priority

Prefer queries in this order (accessibility-first):

1. **getByRole** - Most accessible (screen readers use roles)
2. **getByLabelText** - Good for form inputs
3. **getByText** - Good for static content
4. **getByTestId** - Last resort when nothing else works

```javascript
// BEST: Use semantic role
const button = canvas.getByRole('button', { name: /submit/i });

// GOOD: Use label for inputs
const email = canvas.getByLabelText('Email address');

// OK: Use text for static content
const heading = canvas.getByText('Welcome');

// LAST RESORT: Use test ID
const custom = canvas.getByTestId('custom-widget');
```

## Naming Convention

Use `snake_case` for query variables:

```javascript
// GOOD
const date_button = canvas.getByRole('button');
const menu_item = document.querySelector('[data-value="item"]');
const selected_date = document.querySelector('[aria-current="date"]');

// BAD
const dateButton = canvas.getByRole('button');
const menuItem = document.querySelector('[data-value="item"]');
```

## Related Notes
- [Portaled Components](/storybook/play-functions/play-functions-portaled-components.md) - Querying portaled elements
- [Waiting Strategies](/storybook/play-functions/play-functions-waiting-strategies.md) - Waiting for async assertions
- [Play Functions](/storybook/play-functions/play-functions.md) - Overview
