# Storybook Decorators

Decorators wrap story components to provide layout constraints, context providers, or other environmental setup.

## When to Use Decorators

Use decorators when you need to:

- **Apply layout constraints** (width, height, padding)
- **Provide React context** (theme, state providers)
- **Mock environment** (localStorage, user types)
- **Apply consistent styling** across all stories in a file

## Standard Decorator Patterns

### Layout Constraints (Width/Padding)

For components that need specific dimensions:

```javascript
const meta = {
  title: 'components/DatePicker',
  component: DatePicker,
  decorators: [
    (Story) => (
      <div style={{ width: '300px', padding: '20px' }}>
        <Story />
      </div>
    ),
  ],
};
```

**When to use**:
- Components that expand to container width
- Components that need breathing room for dropdowns
- Components better viewed with constraints

**Examples from codebase**:
- src/components/DatePicker.stories.js:26-32
- src/components/MultiSelect.stories.js:26-31

### Full-Height Layout (Tables)

For components that need vertical space:

```javascript
const meta = {
  title: 'components/TransactionsTable',
  component: TransactionsTable,
  parameters: {
    layout: 'fullscreen',
  },
  decorators: [
    (Story) => (
      <div style={{ padding: '20px', height: '100vh' }}>
        <Story />
      </div>
    ),
  ],
};
```

**When to use**:
- Data tables with virtualization
- Full-page layouts
- Components that calculate height dynamically

**Examples from codebase**:
- src/components/TransactionsTable.stories.js:182-188
- src/components/StatementsTable.stories.js:32-38

### Provider Decorators

Wrap components that require React context:

```javascript
import { SnackbarProvider } from './SnackbarProvider';

const meta = {
  title: 'components/ToastButton',
  decorators: [
    (Story) => (
      <SnackbarProvider>
        <Story />
      </SnackbarProvider>
    ),
  ],
};
```

**When to use**:
- Components using context (theme, state)
- Components requiring providers (router, i18n)
- Notification/modal systems

**Example from codebase**:
- src/components/SnackbarProvider.stories.js:128-131

### User Type Mocking

Mock different user types via localStorage:

```javascript
// File: Component.StaffUser.stories.js
const meta = {
  title: 'components/Component/StaffUser',
  decorators: [
    (Story) => {
      localStorage.setItem('developer-mode', 'true');
      return <Story />;
    },
  ],
};
```

```javascript
// File: Component.stories.js (App User)
const meta = {
  title: 'components/Component/AppUser',
  decorators: [
    (Story) => {
      localStorage.removeItem('developer-mode');
      return <Story />;
    },
  ],
};
```

**When to use**:
- Components with different behavior per user type
- Permission-based UI variations

## Decorator Placement

### Meta-Level Decorators

Apply to ALL stories in the file:

```javascript
const meta = {
  title: 'components/ComponentName',
  component: ComponentName,
  decorators: [
    // Applied to every story
    (Story) => <div style={{ padding: '20px' }}><Story /></div>,
  ],
};
```

### Story-Level Decorators

Apply to ONE specific story:

```javascript
export const SpecialCase = {
  decorators: [
    (Story) => <div style={{ background: '#f0f0f0' }}><Story /></div>,
  ],
  args: { ... },
};
```

**When to use story-level decorators**:
- One story needs different environment than others
- Testing edge cases with specific context
- Rare; prefer meta-level for consistency

## Anti-Patterns to Avoid

### ❌ Inline Styles for Complex Layouts

```javascript
// BAD: Overly complex inline styles
decorators: [
  (Story) => (
    <div style={{
      width: '600px',
      height: '400px',
      padding: '20px',
      margin: '0 auto',
      border: '1px solid #ccc',
      borderRadius: '8px',
      boxShadow: '0 2px 8px rgba(0,0,0,0.1)',
    }}>
      <Story />
    </div>
  ),
],
```

```javascript
// GOOD: Simple constraints, use parameters for complex needs
decorators: [
  (Story) => (
    <div style={{ width: '600px', padding: '20px' }}>
      <Story />
    </div>
  ),
],
parameters: {
  layout: 'centered',
},
```

### ❌ Different Decorators Across Similar Components

Maintain consistency across similar component types:

```javascript
// BAD: DatePicker and TimePicker have different widths
// DatePicker.stories.js
decorators: [(Story) => <div style={{ width: '300px' }}><Story /></div>],

// TimePicker.stories.js
decorators: [(Story) => <div style={{ width: '250px' }}><Story /></div>],

// GOOD: Same width for similar components
// Both use width: '300px'
```

### ❌ Decorators When Parameters Suffice

```javascript
// BAD: Unnecessary decorator
decorators: [
  (Story) => (
    <div style={{ display: 'flex', justifyContent: 'center' }}>
      <Story />
    </div>
  ),
],

// GOOD: Use layout parameter
parameters: {
  layout: 'centered',
},
```

## When NOT to Use Decorators

Skip decorators for:

- **Simple components** that work fine without constraints (AlertBox, Pagination)
- **Full-width components** that naturally expand (Page layouts)
- **Cases where parameters.layout is sufficient**

**Examples without decorators**:
- src/components/Table.stories.js (uses parameters only)
- src/components/AlertBox.stories.js (no decorators needed)

## Combining Decorators and Parameters

Decorators and layout parameters work together:

```javascript
const meta = {
  parameters: {
    layout: 'fullscreen',  // Removes Storybook padding
  },
  decorators: [
    (Story) => (
      <div style={{ height: '100vh', padding: '20px' }}>
        <Story />
      </div>
    ),
  ],
};
```

- `layout: 'fullscreen'` removes Storybook's default padding
- Decorator adds back controlled padding and height

## Related Notes
- [Meta Structure](./meta-structure.md) - Story file configuration
- [ArgTypes Standards](./argtypes-standards.md) - Props documentation
- [Storybook Mock Data](./storybook-mock-data.md) - Mock data patterns
