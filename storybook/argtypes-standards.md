# Storybook ArgTypes Standards

ArgTypes define the controls and documentation for component props in Storybook.

## Coverage Requirements

**Standard**: Comprehensive argTypes for complex components, minimal for UI primitives.

### Complex Components (Full ArgTypes)

Components with significant interaction or configuration should document all props:

- **Forms and inputs** (DatePicker, MultiSelect, FilterField)
- **Data display** (Table, TransactionsTable)
- **Dialogs and modals** (ExportConfirmDialog)
- **Composed components** (FileDropzone, PageHeader)

### UI Primitives (Minimal ArgTypes)

Simple visual components can have minimal argTypes:

- **Basic elements** (AlertBox, Pagination)
- **Static displays** (ErrorMessage)
- **Simple wrappers**

**Examples**:
- Complex: src/components/Table.stories.js:21-41 (4 props, full details)
- Minimal: src/components/AlertBox.stories.js:26-34 (2 props only)

## Standard ArgType Structure

```javascript
argTypes: {
  prop_name: {
    control: 'text' | 'select' | 'boolean' | 'number' | 'object',
    description: 'Human-readable description of the prop',
    options: ['option1', 'option2'],  // For select controls
    table: {
      defaultValue: { summary: 'default_value' },
    },
  },
}
```

## Control Types

### Text Input

```javascript
argTypes: {
  file_name: {
    control: 'text',
    description: 'Name of the uploaded file',
  },
}
```

### Select Dropdown

```javascript
argTypes: {
  variant: {
    control: 'select',
    options: ['plain', 'outlined', 'soft', 'solid'],
    description: 'Visual style variant',
  },
}
```

**Alternative syntax**:
```javascript
argTypes: {
  variant: {
    control: { type: 'select', options: ['plain', 'outlined', 'soft', 'solid'] },
    description: 'Visual style variant',
  },
}
```

### Boolean Checkbox

```javascript
argTypes: {
  show_icon: {
    control: 'boolean',
    description: 'Whether to display the alert icon',
  },
}
```

### Number Spinner

```javascript
argTypes: {
  page_count: {
    control: 'number',
    description: 'Total number of pages',
  },
}
```

**With range**:
```javascript
argTypes: {
  page_count: {
    control: { type: 'number', min: 1, max: 100 },
    description: 'Total number of pages (1-100)',
  },
}
```

### Object Editor

```javascript
argTypes: {
  data: {
    control: 'object',
    description: 'Array of data objects to display',
  },
}
```

## Table Metadata

Document default values in the controls panel:

```javascript
argTypes: {
  size: {
    control: 'select',
    options: ['sm', 'md', 'lg'],
    description: 'Size of the component',
    table: {
      defaultValue: { summary: 'md' },
    },
  },
}
```

## Action Callbacks

For event handler props, use `action` and `fn()`:

```javascript
import { fn } from '@storybook/test';

const meta = {
  argTypes: {
    on_close: {
      action: 'closed',
      description: 'Callback when dialog is closed',
    },
    on_confirm: {
      action: 'confirmed',
      description: 'Callback when export is confirmed',
    },
  },
  args: {
    on_close: fn(),
    on_confirm: fn(),
  },
};
```

**Example**: src/components/ExportConfirmDialog.stories.js:61-77

## Real Example: Table Component

Full argTypes for a complex component:

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

Source: src/components/Table.stories.js:21-41

## Real Example: DatePicker Component

ArgTypes with table metadata:

```javascript
argTypes: {
  default_value: {
    control: 'text',
    description: 'Pre-selected date in YYYY-MM-DD format',
    table: {
      defaultValue: { summary: 'undefined' },
    },
  },
  on_change: {
    action: 'date changed',
    description: 'Callback fired when date selection changes',
  },
  placeholder: {
    control: 'text',
    description: 'Placeholder text shown when no date is selected',
    table: {
      defaultValue: { summary: 'Select a date' },
    },
  },
}
```

Source: src/components/DatePicker.stories.js:33-50

## Minimal Example: AlertBox

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

Source: src/components/AlertBox.stories.js:26-34

## When to Omit ArgTypes

Skip argTypes for:

- Props that are always passed programmatically (rarely changed)
- Internal state props
- Complex callback props that can't be controlled
- Children/render props (better shown in stories)

## Naming Convention

Use `snake_case` for argType keys (matches your variable naming standard):

```javascript
// GOOD
argTypes: {
  file_name: { ... },
  on_change: { ... },
  show_icon: { ... },
}

// BAD
argTypes: {
  fileName: { ... },
  onChange: { ... },
  showIcon: { ... },
}
```

## Related Notes
- [Meta Structure](/storybook/meta-structure.md) - Story file configuration
- [Story Naming](/storybook/story-naming.md) - User journey naming
- [Decorators](/storybook/decorators.md) - Layout and context providers
