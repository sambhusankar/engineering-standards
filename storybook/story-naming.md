# Storybook Story Naming

Story names should follow the User Journey pattern, describing when/why a user sees the component in this state.

## Naming Pattern

**Format**: `<UserAction/State>` or `With<EdgeCaseCondition>`

Each story name should answer: "When/why does a user see this?"

## Action-Based Names

Stories representing user actions or states:

```javascript
// User performed an action and sees result
export const AfterImportingTransactions = { ... }
export const AfterSelectingDate = { ... }
export const AfterUploadingFile = { ... }
export const AfterClickingExport = { ... }

// User is in a state before action
export const BeforeSelectingDate = { ... }
export const BeforeUploadingFile = { ... }

// User is experiencing a temporary state
export const WhileExporting = { ... }
export const WhileUploading = { ... }
export const WhileFileIsProcessing = { ... }
```

**Examples from codebase**:
- `AfterImportingTransactions` - src/components/TransactionsTable.stories.js:206
- `AfterSelectingDate` - src/components/DatePicker.stories.js:150
- `WhileUploading` - src/components/FileDropzone.stories.js:183

## Condition-Based Names

Stories for specific conditions or edge cases:

```javascript
// Data state conditions
export const WhenNoTransactionsExist = { ... }
export const WhenNoStatementsUploaded = { ... }

// Error conditions
export const WhenParserFails = { ... }
export const WhenUploadFails = { ... }

// Edge case conditions
export const WithMissingCategories = { ... }
export const WithLargeDataset = { ... }
export const WithUnusualFileName = { ... }
export const WithVeryLongText = { ... }
```

**Examples from codebase**:
- `WhenNoTransactionsExist` - src/components/TransactionsTable.stories.js:225
- `WithMissingCategories` - src/components/TransactionsTable.stories.js:246
- `WithLargeDataset` - src/components/TransactionsTable.stories.js:267

## Required Story: DeveloperInteractive

**Every story file must include a `DeveloperInteractive` story** for manual testing:

```javascript
/**
 * DEVELOPER TESTING STORY
 *
 * Interactive story with no play functions or auto-interactions.
 * Use this for manual testing and experimentation.
 */
export const DeveloperInteractive = {
  args: {
    // Minimal or realistic default args
  },
};
```

This story allows developers to manually interact with the component without automated play functions interfering.

**Examples from codebase**:
- src/components/DatePicker.stories.js:283
- src/components/TransactionsTable.stories.js:327
- src/components/FileDropzone.stories.js:261
- src/components/MultiSelect.stories.js:384

## Anti-Patterns to Avoid

### ❌ Technical State Names

```javascript
// BAD: Technical implementation details
export const LocationNull = { ... }
export const ParserTypeNull = { ... }
export const IsLoadingTrue = { ... }

// GOOD: User-focused states
export const WhileFileIsProcessing = { ... }
export const WhenParserFails = { ... }
export const WhileDataLoading = { ... }
```

### ❌ Generic Variation Names

```javascript
// BAD: Component variations (old pattern)
export const Default = { ... }
export const Primary = { ... }
export const Secondary = { ... }
export const EmptyState = { ... }

// GOOD: User journey states
export const AfterSuccessfulUpload = { ... }
export const BeforeUserAction = { ... }
export const WhenNoDataExists = { ... }
```

### ❌ Data Type Variations (when UI doesn't change)

```javascript
// BAD: File type doesn't change the UI
export const PDFFile = { ... }
export const CSVFile = { ... }
export const XLSXFile = { ... }

// GOOD: If UI actually differs by type
export const WithPDFPreview = { ... }
export const WithTextPreview = { ... }
```

## Story Count Guidelines

Based on component complexity:

- **Atoms** (buttons, inputs): 1-2 stories
- **Molecules** (cards, dialogs): 3-6 stories
- **Organisms** (forms, tables): 5-10 stories

## Typical Story Set (5-6 stories)

For a standard component:

1. **Happy Path** - Most common user scenario
2. **Empty/Before State** - Before user interaction
3. **Error State** - When something goes wrong
4. **Edge Case 1** - Real production scenario
5. **Edge Case 2** - Real production scenario (optional)
6. **DeveloperInteractive** - Manual testing (required)

## Real Example: FileDropzone (6 stories)

```javascript
export const ReadyToUpload = { ... }              // Happy path: Initial state
export const AfterSelectingFiles = { ... }        // After user selects files
export const AfterSelectingMultipleFiles = { ... }// Edge case: Multiple files
export const WhileUploading = { ... }             // Temporary state
export const WhenUploadFails = { ... }            // Error state
export const DeveloperInteractive = { ... }       // Manual testing
```

Source: src/components/FileDropzone.stories.js

## PascalCase Convention

All story names use PascalCase:

```javascript
// GOOD
export const AfterImportingTransactions = { ... }
export const WhenNoDataExists = { ... }

// BAD
export const afterImportingTransactions = { ... }
export const when_no_data_exists = { ... }
```

## Related Notes
- [Storybook User Journey Pattern](/storybook/storybook-user-journey-pattern.md) - Overall philosophy
- [File Header Pattern](/storybook/documentation/file-header-pattern.md) - JSDoc file headers
- [Meta Structure](/storybook/meta-structure.md) - Story file configuration
- [Documentation Patterns](/storybook/documentation/documentation.md) - Multi-layer docs strategy
