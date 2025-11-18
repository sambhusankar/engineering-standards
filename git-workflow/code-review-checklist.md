# Code Review Checklist

What to check when reviewing code to ensure quality and consistency.

## Core Checks

### Correctness
- [ ] Code does what it's supposed to do
- [ ] Edge cases are handled
- [ ] No obvious bugs or logic errors

### Testing
- [ ] Tests are comprehensive
- [ ] Tests actually test the right things
- [ ] All tests pass
- [ ] Coverage meets requirements

### Code Style
- [ ] Follows naming conventions
- [ ] Consistent with project style
- [ ] No unnecessary complexity
- [ ] Code is readable and maintainable

### Performance
- [ ] No obvious performance issues
- [ ] Appropriate algorithms/data structures
- [ ] Database queries optimized
- [ ] No unnecessary re-renders (React)

### Security
- [ ] No security vulnerabilities
- [ ] Input validation present
- [ ] No secrets in code
- [ ] Authentication/authorization correct

### Documentation
- [ ] Code is self-documenting
- [ ] Complex logic has comments
- [ ] API documentation updated
- [ ] README updated if needed

### Error Handling
- [ ] Errors are caught appropriately
- [ ] Error messages are helpful
- [ ] Logging is appropriate
- [ ] Failures degrade gracefully

## Review Comment Prefixes

Use conventional prefixes for clarity:

- `nit:` - Minor style/formatting suggestion
- `question:` - Asking for clarification
- `suggestion:` - Optional improvement
- `issue:` - Must be addressed before merging

## Good vs Bad Comments

```markdown
❌ Bad: "This is wrong"
✅ Good: "This will fail when user is null. Consider adding a null check."

❌ Bad: "Refactor this"
✅ Good: "This function is doing too much. Consider extracting the validation logic."

❌ Bad: "Use map instead"
✅ Good: "We can simplify this loop using Array.map() for better readability."
```

## Related Notes
- [Pull Request Guidelines](./pull-request-guidelines.md)
- [Commit Message Format](./commit-format.md)
- [Code Style Standards](../naming/variables-snake-case.md)
