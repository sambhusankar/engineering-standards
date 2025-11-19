# Auto-Extract Description from JSDoc (Future Enhancement)

Exploration of automatically extracting `parameters.description` from JSDoc comments to eliminate duplication.

## The Duplication Problem

Currently, story context appears in two places:

```javascript
/**
 * USER JOURNEY STORY: After Creating Org
 *
 * When: User successfully created an organization without adding a custom description
 * User sees: Organization card with name and creation date
 * Tests: Normal card display, date formatting, hover states, navigation link
 */
export const AfterCreatingOrg = {
  parameters: {
    description: 'Happy path: User views their organization card without custom description. Shows organization name and creation date. This is the most common scenario. Card is clickable and navigates to org dashboard.',
  },
  args: { /* ... */ },
};
```

**Issue**: The JSDoc comment and `parameters.description` contain similar information, creating maintenance burden.

## Proposed Solution: Auto-Extraction

Automatically extract `parameters.description` from JSDoc using a custom Storybook loader/plugin.

### Approach 1: Extract from @description Tag

```javascript
/**
 * USER JOURNEY STORY: After Creating Org
 *
 * @description Happy path: User views their organization card without custom description. Shows organization name and creation date.
 *
 * When: User successfully created an organization without adding a custom description
 * User sees: Organization card with name and creation date
 * Tests: Normal card display, date formatting, hover states
 */
export const AfterCreatingOrg = {
  // parameters.description would be auto-populated from @description tag
  args: { /* ... */ },
};
```

### Approach 2: Extract First Paragraph

Automatically extract the first paragraph after the story title:

```javascript
/**
 * After Creating Org
 *
 * Happy path: User views their organization card without custom description.
 * Shows organization name and creation date.
 *
 * Additional details would not be extracted...
 */
export const AfterCreatingOrg = {
  // parameters.description = "Happy path: User views their organization card without custom description. Shows organization name and creation date."
  args: { /* ... */ },
};
```

## Implementation Requirements

This would require building a custom Storybook plugin that:

1. **Parses story files** - Read `.stories.js` files before Storybook processes them
2. **Extracts JSDoc** - Parse JSDoc comments above each story export
3. **Identifies description** - Extract either `@description` tag or first paragraph
4. **Injects parameter** - Programmatically add `parameters.description` to story object
5. **Preserves explicit descriptions** - Allow manual `parameters.description` to override

## Technical Approaches

### Option A: Webpack Loader

Create a webpack loader that transforms story files:

```javascript
// .storybook/loaders/extract-description-loader.js
module.exports = function(source) {
  // Parse JSDoc comments
  // Extract descriptions
  // Inject parameters.description into story objects
  return modifiedSource;
};
```

### Option B: Storybook Addon

Create a Storybook addon that processes stories at runtime:

```javascript
// .storybook/addons/auto-description/preset.js
export function managerEntries(entry = []) {
  return [...entry, require.resolve('./register')];
}
```

### Option C: Babel Plugin

Use a Babel plugin to transform story exports:

```javascript
// babel-plugin-extract-story-description.js
module.exports = function({ types: t }) {
  return {
    visitor: {
      ExportNamedDeclaration(path) {
        // Extract JSDoc from export
        // Inject parameters.description
      },
    },
  };
};
```

## Benefits If Implemented

- **Reduce duplication** - Single source of truth for story context
- **Faster authoring** - Write JSDoc once, description auto-populated
- **Consistency** - JSDoc and banner always match
- **Flexibility** - Can still manually override when needed

## Challenges

- **Parsing complexity** - JSDoc parsing can be fragile
- **Build performance** - Adding processing step may slow builds
- **Edge cases** - Stories without JSDoc, complex formatting
- **Maintenance** - Custom tooling requires ongoing support

## Current Status

**Not implemented**. This remains a future enhancement.

**Current approach**: Manually write both JSDoc and `parameters.description` (accept duplication).

## Related Notes
- [Custom Description Banner](./custom-description-banner.md) - Current manual implementation
- [File Header Pattern](./file-header-pattern.md) - JSDoc file headers
- [Documentation Patterns](./documentation.md) - Overall documentation strategy
