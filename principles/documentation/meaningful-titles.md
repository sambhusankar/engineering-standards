# Meaningful Titles

Clear, descriptive titles that reflect the single idea inside.

## Purpose

Title should be specific enough to determine relevance **without reading content**.

## Format

```
topic/specific-concept.md
```

`topic/` = broad category (naming, testing, architecture)
`specific-concept` = exact pattern/practice being documented

## Good Examples

✅ `testing/aaa-pattern.md` - Arrange-Act-Assert test structure
✅ `architecture/container-presentational-pattern.md` - Separating logic from UI
✅ `git-workflow/branch-naming.md` - Branch naming conventions
✅ `naming/variables-snake-case.md` - Variable naming with snake_case

Each title precisely describes the single concept inside.

## Bad Examples

❌ `code-style.md` - What aspect of code style?
❌ `components.md` - Which component pattern?
❌ `testing.md` - What about testing?
❌ `react-patterns.md` - Covers multiple patterns

Can't determine relevance without reading content.

## Filename Conventions

✅ `container-presentational-pattern.md` (hyphens, lowercase)
❌ `container_presentational_pattern.md` (underscores)
❌ `ContainerPresentationalPattern.md` (PascalCase)

✅ `auto-open-pattern.md` (descriptive)
❌ `ao-pattern.md` (cryptic)

## Testing Your Title

**Can someone find this by searching the title?** → Add keywords if no
**Does the title describe the content accurately?** → Rename if no
**Is it specific enough to determine relevance?** → Be more specific if no

## Related Notes
- [Single Idea Per Note](./single-idea-per-note.md)
- [Brevity](./brevity.md)
