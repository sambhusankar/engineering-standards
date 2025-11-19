# Pull Request Guidelines

Create focused, well-documented pull requests that are easy to review.

## PR Title

Follow commit message format:
```
feat: add user authentication system
fix: resolve login redirect issue
docs: update API documentation
```

## PR Size

- **Ideal**: 200-400 lines changed
- **Maximum**: 800 lines changed
- **Too large?** Split into multiple PRs

## Description Template

```markdown
## Summary
Brief description of what this PR does and why.

## Changes
- Added user authentication module
- Created login/logout API endpoints
- Implemented session management
- Added authentication middleware

## Type of Change
- [ ] New feature
- [ ] Bug fix
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] E2E tests added/updated
- [ ] Manual testing completed

## Screenshots (if applicable)
[Add screenshots for UI changes]

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests pass locally
- [ ] Documentation updated
- [ ] No new warnings or errors
- [ ] Reviewers assigned

## Related Issues
Fixes #123
Related to #456
```

## Breaking Up Large Changes

1. **Foundational PR** - Data models, types
2. **Implementation PR** - Core logic
3. **UI PR** - User interface
4. **Testing PR** - Comprehensive tests

## PR Labels

- `feature` - New functionality
- `bug` - Bug fixes
- `documentation` - Docs changes
- `breaking-change` - Breaking changes
- `needs-review` - Ready for review
- `ready-to-merge` - Approved

## Related Notes
- [Code Review Checklist](/git-workflow/code-review-checklist.md)
- [Commit Message Format](/git-workflow/commit-format.md)
- [Branch Naming](/git-workflow/branch-naming.md)
