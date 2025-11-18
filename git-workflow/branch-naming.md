# Branch Naming Convention

Use descriptive, hyphenated branch names with type prefix for clarity.

## Format

`<type>/<short-description>`

## Types

- `feature/` - New features
- `fix/` or `bugfix/` - Bug fixes
- `hotfix/` - Critical production fixes
- `chore/` - Maintenance tasks
- `refactor/` - Code refactoring
- `docs/` - Documentation only
- `test/` - Test additions/changes

## Examples

```bash
git checkout -b feature/user-authentication
git checkout -b fix/email-validation-error
git checkout -b hotfix/security-patch
git checkout -b chore/update-eslint-config
git checkout -b refactor/extract-api-service
git checkout -b docs/update-readme
git checkout -b test/add-unit-tests
```

## Best Practices

- Use lowercase
- Use hyphens, not underscores or spaces
- Keep descriptions concise (2-4 words)
- Be specific about what the branch does

## Good vs Bad

```bash
# Good
feature/oauth-integration
fix/login-redirect-bug
refactor/user-service

# Bad
feature/new-stuff
fix/bug
my-branch
johns-work
```

## Related Notes
- [Commit Message Format](./commit-format.md)
- [Git Workflow Strategy](./git-flow-vs-github-flow.md)
- [Creating Branches](./creating-branches.md)
