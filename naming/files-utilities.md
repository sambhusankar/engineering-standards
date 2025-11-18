# Utility Files: camelCase or kebab-case

Utility and helper files use `camelCase` or `kebab-case` depending on project convention.

## Patterns

**camelCase** (preferred for single-concept files):
```
fetchAPI.js
dateFormatter.js
stringUtils.js
```

**kebab-case** (preferred for multi-word descriptive names):
```
fetch-api.js
date-formatter.js
string-utils.js
```

## Choose One Per Project

Pick one convention and apply consistently throughout the project. Don't mix:

```
// Bad - mixing conventions
src/utils/
├── fetchAPI.js
├── date-formatter.js
├── stringUtils.js

// Good - consistent camelCase
src/utils/
├── fetchAPI.js
├── dateFormatter.js
├── stringUtils.js

// Also good - consistent kebab-case
src/utils/
├── fetch-api.js
├── date-formatter.js
├── string-utils.js
```

## Named Exports Typical

Utility files typically use named exports:

```javascript
// stringUtils.js or string-utils.js
export function capitalize(str) { }
export function truncate(str, length) { }
```

## Related Notes
- [Files: Components](./files-components.md)
- [Files: Tests](./files-tests.md)
- [Named vs Default Exports](../architecture/named-vs-default-exports.md)
