# Commit Message Format: Conventional Commits

Use Conventional Commits format for clear, parseable commit messages.

## Format

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Types

- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `style:` - Code style changes (formatting, no logic change)
- `refactor:` - Code refactoring
- `test:` - Test additions/changes
- `chore:` - Build process or tool changes
- `perf:` - Performance improvements

## Examples

**Simple commits:**
```bash
git commit -m "feat: add user authentication"
git commit -m "fix: resolve email validation error"
git commit -m "docs: update API documentation"
git commit -m "refactor: extract user service from controller"
git commit -m "test: add unit tests for calculateTotal"
git commit -m "chore: update dependencies"
```

**With scope:**
```bash
git commit -m "feat(auth): add OAuth2 integration"
git commit -m "fix(api): handle network timeout errors"
git commit -m "test(components): add Button tests"
```

**With body and footer:**
```bash
git commit -m "fix: prevent race condition in user login

Added mutex lock to prevent concurrent login attempts
from the same user causing database conflicts.

Fixes #123"
```

## Best Practices

**Write clear messages:**
- Use imperative mood ("add feature" not "added feature")
- First line under 50 characters
- Body lines under 72 characters

**Make atomic commits:**
- One logical change per commit
- Commit should be self-contained
- Should build and pass tests

## Related Notes
- [Branch Naming](./branch-naming.md)
- [Pull Request Guidelines](./pull-request-guidelines.md)
- [Commit Frequently](./commit-best-practices.md)
