# Test Coverage Requirements

Maintain minimum 80% code coverage with 100% coverage for critical paths.

## Coverage Targets

- **Unit tests**: 80% minimum
- **Critical paths**: 100% required
- **Edge cases**: Explicitly tested

## What Counts as Critical

**Must have 100% coverage:**
- Business logic and calculations
- Authentication and authorization
- Payment processing
- Data transformations
- API request/response handling

**Should have 80%+ coverage:**
- UI components
- Utility functions
- Database queries
- Error handling

**Can skip:**
- Third-party library thin wrappers
- Simple getters/setters
- Configuration files

## Configuration Example

Jest configuration:

```javascript
// jest.config.js
module.exports = {
  collectCoverageFrom: [
    'src/**/*.{js,jsx,ts,tsx}',
    '!src/**/*.stories.{js,jsx}',
    '!src/**/*.test.{js,jsx}',
  ],
  coverageThresholds: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80,
    },
  },
};
```

## Metrics Tracked

- **Line coverage**: Percentage of lines executed
- **Branch coverage**: Percentage of branches (if/else) executed
- **Function coverage**: Percentage of functions called

## Related Notes
- [Unit vs Integration Tests](./unit-vs-integration.md)
- [What to Test](./what-to-test.md)
- [Testing Critical Paths](./testing-critical-paths.md)
