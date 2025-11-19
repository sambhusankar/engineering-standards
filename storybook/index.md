# Storybook Standards

Atomic notes on Storybook patterns for component testing and documentation using the User Journey approach.

## Philosophy

Storybook stories represent **real user scenarios**, not technical component variations. Each story answers: "When/why does a user see this component?"

This approach provides:
- Product documentation that maps to user needs
- Reduced cognitive load with clear story purposes
- Fast maintenance by identifying which scenarios need updates
- Better QA by mapping stories to production scenarios

## Core Patterns

### User Journey & Naming
- [Storybook User Journey Pattern](./storybook-user-journey-pattern.md) - Complete philosophy and methodology
- [Story Naming](./story-naming.md) - PascalCase user journey naming conventions

### Story File Structure
- [Meta Structure](./meta-structure.md) - Standard meta object format
- [ArgTypes Standards](./argtypes-standards.md) - Documenting props with controls
- [Decorators](./decorators.md) - Layout constraints and context providers

### Documentation
- [documentation/](./documentation/) - Multi-layer documentation patterns
  - [Documentation Patterns](./documentation/documentation.md) - Overall strategy
  - [File Header Pattern](./documentation/file-header-pattern.md) - JSDoc headers for story files
  - [Custom Description Banner](./documentation/custom-description-banner.md) - Story descriptions in canvas
  - [MDX Component Docs](./documentation/mdx-component-docs.md) - Component-level MDX documentation
  - [Auto-Extract Description from JSDoc](./documentation/auto-extract-description-from-jsdoc.md) - Future enhancement

### Interaction & Testing
- [play-functions/](./play-functions/) - Automated interaction patterns
  - [Play Functions](./play-functions/play-functions.md) - Overview
  - [When to Use](./play-functions/play-functions-when-to-use.md) - Decision guidelines
  - [Auto-Open Pattern](./play-functions/play-functions-auto-open-pattern.md) - Auto-opening collapsed UI
  - [Portaled Components](./play-functions/play-functions-portaled-components.md) - Handling portaled elements
  - [Waiting Strategies](./play-functions/play-functions-waiting-strategies.md) - Async waiting patterns
  - [Assertions](./play-functions/play-functions-assertions.md) - Testing and queries
- [Storybook Interactions](./storybook-interactions.md) - Detailed interaction patterns

### Data & Mocking
- [Storybook Mock Data](./storybook-mock-data.md) - Centralized mocks and action proxies

## Quick Reference

### Story File Checklist

When creating or reviewing story files:

- [ ] File header with user journey pattern explanation
- [ ] Meta uses `const meta = {...}; export default meta;` pattern
- [ ] Stories named with user journey pattern (`AfterAction`, `BeforeState`, etc.)
- [ ] Each story has `parameters.description` for canvas banner
- [ ] **Parameters before args** in story objects
- [ ] Play functions for interactive components (if appropriate)
- [ ] `DeveloperInteractive` story included (no play function)
- [ ] (Optional) Create `.mdx` file for component-level docs

### Component Type Guidelines

| Component Type | Play Functions | Story Focus | Story Count |
|---------------|----------------|-------------|-------------|
| Forms/Inputs | ✅ Yes | Interactions, validation | 3-6 |
| Dialogs/Modals | ✅ Yes | User actions, workflows | 3-6 |
| Tables/Lists | ❌ No | Data states, edge cases | 5-10 |
| Cards/Display | ❌ No | Visual states | 1-3 |
| Buttons/Atoms | Optional | Visual variants | 1-2 |

### Naming Conventions

**Story names** (PascalCase, user journey):
- `AfterImportingTransactions` - User completed an action
- `BeforeSelectingDate` - User hasn't acted yet
- `WhileUploading` - Temporary/loading state
- `WhenNoDataExists` - Conditional state
- `WithLargeDataset` - Edge case condition
- `DeveloperInteractive` - Manual testing (always include)

**Variable names** (snake_case):
```javascript
const auto_open_dropdown = async ({ canvasElement }) => { /* ... */ };
const sample_transactions = [ /* ... */ ];
```

## Getting Started

### For Your First Story File

1. Read: [Storybook User Journey Pattern](./storybook-user-journey-pattern.md) - Understand the philosophy
2. Read: [File Header Pattern](./documentation/file-header-pattern.md) - Add file header
3. Read: [Meta Structure](./meta-structure.md) - Set up meta object
4. Read: [Story Naming](./story-naming.md) - Name your stories
5. Read: [Custom Description Banner](./documentation/custom-description-banner.md) - Add story descriptions

### For Interactive Components

6. Read: [When to Use Play Functions](./play-functions/play-functions-when-to-use.md) - Decide if you need play functions
7. Read: [Play Functions](./play-functions/play-functions.md) - Learn the basics
8. Read specific patterns as needed (auto-open, portals, waiting, assertions)

### For Documentation

9. Read: [Documentation Patterns](./documentation/documentation.md) - Understand the 3-layer strategy
10. Read: [MDX Component Docs](./documentation/mdx-component-docs.md) - Create comprehensive docs (optional)

## Common Use Cases

### "How do I name this story?"
→ [Story Naming](./story-naming.md)

### "Should I add a play function?"
→ [When to Use Play Functions](./play-functions/play-functions-when-to-use.md)

### "How do I document this component?"
→ [Documentation Patterns](./documentation/documentation.md)

### "How do I handle dropdown menus?"
→ [Auto-Open Pattern](./play-functions/play-functions-auto-open-pattern.md) + [Portaled Components](./play-functions/play-functions-portaled-components.md)

### "How do I wait for animations?"
→ [Waiting Strategies](./play-functions/play-functions-waiting-strategies.md)

### "What assertions can I use?"
→ [Assertions](./play-functions/play-functions-assertions.md)
