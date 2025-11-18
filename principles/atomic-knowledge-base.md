# Atomic Knowledge Base Principles

This knowledge base is designed for efficient LLM consumption using atomic notes - each note focuses on a single, well-defined concept.

## Core Principles

### 1. Single Idea Per Note

Each note addresses one specific concept, pattern, or practice. Instead of comprehensive documents covering multiple topics, we create focused notes that can be consumed independently.

**Why**: LLM context windows are precious. Loading only relevant atomic notes is more cost-efficient than loading entire comprehensive documents.

**Example**:
- ❌ `code-style.md` covering naming, imports, formatting, comments
- ✅ `naming/variables-camelcase.md`, `imports/order.md`, `formatting/indentation.md`

### 2. Meaningful Titles

Each note has a clear, descriptive title that reflects its single idea. The title should be specific enough that an LLM can determine relevance without reading the content.

**Format**: `topic/specific-concept.md`

**Examples**:
- `testing/aaa-pattern.md` - Arrange-Act-Assert test structure
- `architecture/components/container-presentational-pattern.md` - Separating logic from UI
- `git-workflow/branch-naming.md` - Branch naming conventions

### 3. Links Over Hierarchy

Notes connect to related concepts through links rather than rigid hierarchies. This allows for flexible, emergent organization.

**Pattern**: Reference related notes using relative links:
```markdown
See also:
- [Testing Coverage Requirements](./coverage-requirements.md)
- [Mock External Dependencies](./mock-external-dependencies.md)
```

### 4. Emergent Structure

The knowledge base structure emerges naturally from the atomic notes and their connections. Directory organization provides loose grouping, but the true structure comes from inter-note links.

**Benefits**:
- Discover unexpected connections between concepts
- Navigate knowledge graph through related topics
- Add new notes without disrupting existing structure

## Benefits for LLM Consumption

### Overcome Information Overload
Break information into manageable, context-sized pieces. Load only what's needed for the current task.

### Improve Knowledge Retention
When LLMs need to process information across conversations, smaller atomic units are easier to reference consistently.

### Discover New Connections
The graph of linked notes can reveal relationships between concepts that might not be obvious in hierarchical documentation.

### Cost Efficiency
Only consume the tokens needed for the specific concept being applied, rather than loading entire comprehensive guides.

## Writing Atomic Notes

### Structure
```markdown
# [Clear Descriptive Title]

[Concise explanation of single concept - typically 20-50 lines]

## Example
[Code example if applicable - inline for simple cases]

## Related Notes
- [Link to related concept 1](../path/to/note.md)
- [Link to related concept 2](./related-note.md)
```

### Guidelines
- **Focus**: One concept, fully explained
- **Brevity**: Usually 20-50 lines total
- **Clarity**: Self-contained explanation
- **Examples**: Inline code for simple cases, link to `/examples/` for complex ones
- **Links**: Connect to 2-5 related notes

## Discovery Mechanisms

### For LLMs
1. **Search by topic**: Use directory structure (e.g., `testing/`, `naming/`)
2. **Index files**: Check `index.md` in each directory for overview
3. **Follow links**: Navigate through related notes
4. **Keyword search**: Search note titles and content

### For Humans
1. Start with directory `index.md` files
2. Browse related notes through links
3. Use git grep or IDE search for keywords

## Related Notes
- [Repository Structure](../README.md)
- [Contributing Guidelines](../CLAUDE.md)

---

This atomic approach enables efficient, context-aware knowledge retrieval for LLM-driven development tools.
