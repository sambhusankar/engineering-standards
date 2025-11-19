# Storybook Meta Structure

Standard structure for the meta object in Storybook story files using CSF v3.

## Standard Meta Object Pattern

```javascript
const meta = {
  title: 'components/ComponentName',
  component: ComponentName,
  parameters: {
    layout: 'padded',
  },
  argTypes: {
    // See argtypes-standards.md
  },
  decorators: [
    // See decorators.md
  ],
};

export default meta;
```

**Note**: We do NOT use Storybook's autodocs feature. For component-level documentation, create `.mdx` files (see [MDX Component Docs](./documentation/mdx-component-docs.md)).

## Title Convention

**Standard**: Match the folder structure exactly, lowercase `components/`

```javascript
// Component at: src/components/DatePicker.jsx
title: 'components/DatePicker'

// Component at: src/components/Filters/FilterField.jsx
title: 'components/Filters/FilterField'

// Page component at: src/app/orgs/[o_id]/transactions/_components/TransactionsTable.jsx
title: 'app/orgs/[o_id]/transactions/TransactionsTable'
```

**Rationale**: Titles should mirror the actual folder structure for consistency and easy navigation in Storybook's sidebar.

## Export Style

**Standard**: Always use the `const meta` variable pattern

```javascript
// GOOD: Clear and explicit
const meta = {
  title: 'components/Table',
  component: Table,
  parameters: { ... },
};

export default meta;
```

```javascript
// AVOID: Inline export
export default {
  title: 'components/Table',
  component: Table,
};
```

**Rationale**: The `const meta` pattern makes it easier to reference metadata within the file and is more maintainable.

## Parameters Structure

### Layout Parameter

Choose layout based on component type:

```javascript
parameters: {
  layout: 'padded',      // Forms, cards, most components
  layout: 'centered',    // Small standalone widgets (DatePicker, Pagination)
  layout: 'fullscreen',  // Data tables, full-page components
}
```

### Story Description (Custom Banner)

Use `parameters.description` for story-level context displayed in canvas:

```javascript
parameters: {
  description: 'Happy path: User views their organization card. Most common scenario.',
}
```

See [Custom Description Banner](./documentation/custom-description-banner.md) for details.

### Component Documentation

For comprehensive component documentation, create `.mdx` files instead of using autodocs:

See [MDX Component Docs](./documentation/mdx-component-docs.md) for details.

## File Structure Order

Consistent ordering for readability:

```javascript
// 1. Imports
import ComponentName from './ComponentName';
import { OtherComponent } from '@mui/joy';
import { within, userEvent, expect } from '@storybook/test';

// 2. Sample/Mock Data (if needed)
const sample_transactions = [
  { id: 1, amount: 100 },
  { id: 2, amount: 200 },
];

// 3. Meta Object
const meta = {
  title: 'components/ComponentName',
  component: ComponentName,
  parameters: { ... },
  argTypes: { ... },
  decorators: [ ... ],
};

export default meta;

// 4. Stories (parameters before args)
export const AfterImportingData = {
  parameters: {
    description: 'Happy path: User views data after import. Table shows all transactions.',
  },
  args: { data: sample_transactions },
};

export const DeveloperInteractive = {
  args: {},
};
```

## Multi-User Story Files

When component behavior differs by user type, create separate story files:

```javascript
// File: ComponentName.stories.js
const meta = {
  title: 'components/ComponentName/AppUser',
  // ... app user configuration
};

// File: ComponentName.StaffUser.stories.js
const meta = {
  title: 'components/ComponentName/StaffUser',
  // ... staff user configuration
};
```

## Viewport Parameters (Optional)

For responsive design testing:

```javascript
parameters: {
  viewport: {
    defaultViewport: 'mobile1',
  },
}
```

## Related Notes
- [Story Naming](./story-naming.md) - User journey naming conventions
- [ArgTypes Standards](./argtypes-standards.md) - Props documentation
- [Decorators](./decorators.md) - Layout and context providers
- [Documentation Patterns](./documentation/documentation.md) - Multi-layer docs strategy
- [Custom Description Banner](./documentation/custom-description-banner.md) - Story descriptions
- [File Header Pattern](./documentation/file-header-pattern.md) - JSDoc file headers
