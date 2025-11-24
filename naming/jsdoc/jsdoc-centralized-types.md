# JSDoc: Centralized Type Definitions

For types shared across many files (e.g., database models), create dedicated `.types.js` files organized by domain concern.

## Directory Structure

```
/src/types/
├── database/              # Database model types (one-to-one with ORM models)
│   ├── Statement.types.js
│   ├── Account.types.js
│   └── index.js          # Re-exports all database types
│
├── general/              # General application types
│   ├── RouteParams.types.js
│   └── index.js          # Re-exports all general types
│
└── [future categories]/  # api/, ui/, parser/, etc.
```

## Type Definition File

```javascript
// src/types/database/Statement.types.js
/**
 * Bank statement upload with parsing metadata
 * @typedef {Object} Statement
 * @property {string} id - Statement unique identifier
 * @property {string} org - Organization ID
 * @property {string} file_name - Name of uploaded file
 * @property {string} created_at - ISO timestamp
 * @property {boolean} is_archived - Whether archived
 */

module.exports = {};
```

## Central Re-export Per Category

```javascript
// src/types/database/index.js
/**
 * @typedef {import('./Statement.types').Statement} Statement
 * @typedef {import('./Account.types').Account} Account
 * @typedef {import('./Transaction.types').Transaction} Transaction
 */

module.exports = {};
```

## Using Centralized Types

```javascript
// RenameStatementDialog.jsx
/**
 * @typedef {import('@/types/database').Statement} Statement
 * @typedef {import('@/types/general').RouteParams} RouteParams
 *
 * @param {Object} props
 * @param {Statement} props.statement - Statement to rename
 * @param {RouteParams} props.params - Route parameters
 */
export default function RenameStatementDialog({ statement, params }) {
  // Implementation
}
```

## Type Import Pattern

JSDoc supports type imports using `import()` syntax:

```javascript
// Database types
/**
 * @typedef {import('@/types/database').Statement} Statement
 * @typedef {import('@/types/database').Account} Account
 */

// General types
/**
 * @typedef {import('@/types/general').RouteParams} RouteParams
 */
```

## Benefits

- Single source of truth for domain models
- Full type expansion in editor hover (better than TypeScript .d.ts files)
- Clean import pattern via central index per category
- Pure JavaScript - no TypeScript compilation required
- Organized by domain concern under `/src/types/`
- Scalable structure with room for new type categories

## When to Use Centralized Type Files

- Database models used across many components
- API response/request types (future: `/src/types/api/`)
- UI component types (future: `/src/types/ui/`)
- Shared domain models (User, Organization, etc.)
- Types that represent core business entities

## Type Organization Guidelines

- `/src/types/database/` - Pure database types (one-to-one with ORM models)
- `/src/types/general/` - General application types (routing, config, etc.)
- Keep nested types inline (e.g., `StatementMetadata` inside `Statement.types.js`)
- Create new category directories as needed (`api/`, `ui/`, `parser/`)

## Related Notes
- [JSDoc Inline Types vs @typedef](/naming/jsdoc/jsdoc-inline-vs-typedef.md)
- [JSDoc Type Definitions](/naming/jsdoc/jsdoc-typedef.md)
- [JSDoc Basic Annotations](/naming/jsdoc/jsdoc-basic-annotations.md)
