# Test Type Selection

When to use Jest unit tests vs Storybook component tests vs Playwright E2E tests.

## Decision Guide

**Jest Unit/Integration Tests** - Use for:
- Utility functions
- Data parsers
- API integrations
- Business logic
- Database operations

**Storybook Component Tests** - Use for:
- UI components
- User interactions
- Visual states
- Component isolation

**Playwright E2E Tests** - Use for:
- Critical user flows
- Full-stack integration
- Authentication flows
- Real browser testing

## Testing Philosophy

1. **Test user behavior, not implementation** - Focus on what users see/do
2. **Isolate units completely** - Mock all external dependencies
3. **Test both success and failure paths** - Always test error scenarios
4. **Use semantic mocking** - Mock by meaning (URL), not order
5. **Reset mocks between tests** - jest.clearAllMocks() in beforeEach
6. **Load fixtures once** - Use beforeAll for expensive operations
7. **Create reusable utilities** - DRY principle for test helpers
8. **Stories are user journeys** - Name stories after real scenarios

## Related Notes
- [Unit vs Integration Tests](/testing/unit-vs-integration.md)
- [Testing React Components](/testing/testing-react-components.md)
- [Playwright E2E Structure](/testing/playwright-e2e-structure.md)
