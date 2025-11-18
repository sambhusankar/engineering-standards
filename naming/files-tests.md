# Test Files: Match Source with .test or .spec

Test files should match the source file name with `.test.js` or `.spec.js` suffix.

## Pattern

Source file → Test file:
```
Button.jsx          → Button.test.jsx
UserCard.jsx        → UserCard.spec.jsx
fetchAPI.js         → fetchAPI.test.js
dateFormatter.js    → dateFormatter.spec.js
```

## Convention Choice

**`.test.js`** - More common in Jest ecosystems

**`.spec.js`** - Common in BDD/spec-focused testing

Choose one per project and apply consistently.

## Co-location

Place test files next to source files:

```
src/components/Button/
├── Button.jsx
├── Button.test.jsx
└── Button.stories.jsx
```

Or in `__tests__` directory:

```
src/components/Button/
├── __tests__/
│   └── Button.test.jsx
├── Button.jsx
└── Button.stories.jsx
```

## Integration/E2E Tests

Different pattern for integration and E2E tests:

```
tests/integration/
└── user-api.integration.test.js

e2e/
└── user-registration.spec.js
```

## Related Notes
- [Test File Structure](../testing/test-file-structure.md)
- [Files: Components](./files-components.md)
- [Unit vs Integration Tests](../testing/unit-vs-integration.md)
