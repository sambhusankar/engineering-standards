# Storybook ArgTypes Examples

Real-world examples of argTypes from actual components, demonstrating minimal to comprehensive coverage.

## Example 1: Table Component (Complex)

Full argTypes for a complex component with multiple visual options:

```javascript
argTypes: {
  type: {
    control: 'select',
    options: ['topHeader', 'regular'],
    description: 'Table variant with different header styles',
  },
  size: {
    control: 'select',
    options: ['sm', 'md', 'lg'],
    description: 'Size of table text and spacing',
  },
  variant: {
    control: 'select',
    options: ['plain', 'outlined', 'soft', 'solid'],
    description: 'Visual style variant',
  },
  color: {
    control: 'select',
    options: ['primary', 'neutral', 'danger', 'success', 'warning'],
    description: 'Color scheme',
  },
}
```

**Source**: src/components/Table.stories.js:21-41

## Example 2: AlertBox Component (Minimal)

Simple component with only key props:

```javascript
argTypes: {
  color: {
    control: 'select',
    options: ['primary', 'neutral', 'danger', 'success', 'warning'],
    description: 'Color scheme of the alert',
  },
  show_icon: {
    control: 'boolean',
    description: 'Whether to display the alert icon',
  },
}
```

**Source**: src/components/AlertBox.stories.js:26-34

## Example 3: ExportConfirmDialog (Multiple Callbacks)

Component with multiple event handlers:

```javascript
import { fn } from 'storybook/test';

const meta = {
  argTypes: {
    on_close: { action: 'closed', description: 'Callback when dialog is closed' },
    on_confirm: { action: 'confirmed', description: 'Callback when export is confirmed' },
    file_name: { control: 'text', description: 'Name of the file being exported' },
  },
  args: {
    on_close: fn(),
    on_confirm: fn(),
    file_name: 'transactions.csv',
  },
};
```

**Source**: src/components/ExportConfirmDialog.stories.js:61-77

## Common Patterns

**Minimal (2-3 props)**: Visual variants and key toggles (e.g., AlertBox, Pagination)

**Standard (4-6 props)**: All visual options, primary data props, key callbacks (e.g., Table, DatePicker)

**Comprehensive (7+ props)**: All configurable props, all event handlers, complex data structures (e.g., TransactionsTable, FilterField)

## Related Notes
- [ArgTypes Basics](/storybook/argtypes/basics.md)
- [ArgTypes Control Types](/storybook/argtypes/control-types.md)
- [Action Mocking Pattern](/storybook/mocking/actions.md)
- [Story Naming](/storybook/organization/story-naming.md)
