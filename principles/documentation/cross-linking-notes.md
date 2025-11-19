# Cross-Linking Notes

Connect related notes through links to create a knowledge graph.

## Principle

Atomic notes live in emergent folders (organized by topic). Links connect notes across folder boundaries (related concepts).

Both folders and links emerge naturally over time - neither should be forced or anticipated upfront.

## Folders Organize, Links Connect

**Folders**: Group notes by topic
```
naming/
  variables-snake-case.md
  components-pascalcase.md
testing/
  aaa-pattern.md
  coverage-requirements.md
```

**Links**: Connect related concepts across folders
```
naming/variables-snake-case.md
  → links to → testing/test-naming.md
  → links to → architecture/custom-hooks.md
```

Same note discoverable via both folder (topic) and links (relationships).

## Link Pattern

Use relative paths from current file:

```markdown
## Related Notes
- [Coverage Requirements](./coverage-requirements.md)
- [Mock Dependencies](./mock-external-dependencies.md)
- [Custom Hooks](../architecture/custom-hooks.md)
```

## How Many Links

**Minimum**: 2-3 links (isolated notes aren't discoverable)
**Maximum**: 5-7 links (too many is overwhelming)
**Sweet spot**: 3-5 links per note

## Link Placement

Put related links at **end** of note:

```markdown
# Note Title

[Content here]

## Related Notes
- [Link 1](./link1.md)
- [Link 2](../other-dir/link2.md)
```

## Benefits

**Dual discovery**: Find notes by topic (folder) or relationship (links)
**Cross-topic connections**: Links reveal relationships folder structure can't express
**Emergent knowledge graph**: Network of concepts emerges from cross-references

## Related Notes
- [Emergent Structure](./emergent-structure.md)
- [Discovery Patterns](./discovery-patterns.md)
- [Single Idea Per Note](./single-idea-per-note.md)
