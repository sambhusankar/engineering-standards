# When to Use Play Functions

Guidelines for deciding when to add play functions to Storybook stories.

## Use Play Functions For

### Interactive Components

Components with user interactions that benefit from automated demonstration:

- **Forms and inputs**: DatePicker, MultiSelect, TextInput, FilterField
- **Dialogs and modals**: ExportConfirmDialog, FileDetailsDialog
- **Dropdowns and menus**: Dropdown, Select
- **Upload components**: FileDropzone

**Why**: Auto-opening or interacting with these components provides immediate visual feedback and tests interactions.

**Examples from codebase**:
- src/components/DatePicker.stories.js (5 stories with play functions)
- src/components/MultiSelect.stories.js (5 stories with play functions)
- src/components/FileDropzone.stories.js (has play functions)
- src/components/AlertBox.stories.js (has play functions)

## Skip Play Functions For

### Data Display Components

Components primarily displaying data:

- **Tables**: TransactionsTable, StatementsTable, RulesTable
- **Lists**: TransactionList, AccountList
- **Cards**: DataCard, SummaryCard, OrgCard

**Why**: These components don't require interaction to demonstrate. Focus stories on different data states instead.

**Examples from codebase**:
- src/components/TransactionsTable.stories.js (no play functions)
- src/components/StatementsTable.stories.js (no play functions)
- src/components/Table.stories.js (no play functions)

### DeveloperInteractive Story

**Never add play functions to `DeveloperInteractive`**:

```javascript
/**
 * DEVELOPER TESTING STORY
 */
export const DeveloperInteractive = {
  args: { /* realistic defaults */ },
  // NO play function - reserved for manual testing
};
```

This story is specifically for manual interaction without automated interference.

## Decision Matrix

| Component Type | Play Functions? | Focus Stories On |
|---------------|-----------------|------------------|
| Forms/Inputs | ✅ Yes | Interactions, validation, multi-step workflows |
| Dialogs/Modals | ✅ Yes | Auto-open for visual review, user actions |
| Dropdowns/Menus | ✅ Yes | Auto-open expanded state |
| Tables/Lists | ❌ No | Data states (empty, populated, large) |
| Cards/Display | ❌ No | Visual states, edge cases |
| Static UI | ❌ No | Visual variants only |

## Related Notes
- [Play Functions](./play-functions.md) - Overview and basic structure
- [Auto-Open Pattern](./play-functions-auto-open-pattern.md) - Auto-opening dropdowns/dialogs
- [Storybook User Journey Pattern](./storybook-user-journey-pattern.md) - Story philosophy
