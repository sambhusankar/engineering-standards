# Changelog

All notable changes to the engineering standards will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

### Changed
- **BREAKING**: Variable naming convention changed from camelCase to snake_case
  - `naming/variables-camelcase.md` renamed to `naming/variables-snake-case.md`
  - **Exception**: React hook returns use camelCase (e.g., `const [isEditing, setIsEditing] = useState()`)
  - **Exception**: All variables returned from React hooks use camelCase (e.g., `const { data, error, isLoading } = useQuery()`)
  - Regular variables (derived values, event handler variables, service layer) use snake_case
  - Updated all code examples across architecture/ and testing/ directories
  - Functions remain camelCase, components remain PascalCase
  - React props remain camelCase (React convention)
  - Constants remain SCREAMING_SNAKE_CASE
  - Files exporting modules: camelCase.js (no longer allowing kebab-case)
  - Files exporting components: PascalCase.jsx (unchanged)

### Added
- **JavaScript-First Philosophy**:
  - `principles/javascript-with-jsdoc.md` - New principle documenting JavaScript with JSDoc approach
  - `naming/jsdoc-types.md` - JSDoc type patterns and documentation guide
  - Small team philosophy: minimize complexity, fewer build steps, simpler tooling
- **Functional Programming Philosophy**:
  - `principles/functional-programming.md` - Comprehensive guide on functional vs OOP approach
  - `architecture/module-organization.md` - Organizing code in functional modules
  - Focus on pure functions, immutability, composition over classes
  - Classes only for React Error Boundaries and very specific objects (Date, etc.)

### Changed
- **BREAKING**: Restructured repository from comprehensive documents to atomic knowledge base
  - Replaced long-form standards documents with 38+ focused atomic notes
  - Organized into topic directories: naming/, testing/, architecture/, git-workflow/, principles/
  - Each note addresses single concept (20-50 lines typical)
  - Added index.md files for topic navigation
  - Optimized for LLM consumption and cost efficiency
- **Refactored to Functional Programming**:
  - Updated `architecture/service-layer-pattern.md` - Now uses exported functions instead of objects
  - Updated `naming/jsdoc-types.md` - Replaced class examples with module patterns
  - Updated `architecture/error-boundaries.md` - Noted as exception requiring classes
  - Updated `architecture/arrow-vs-declaration.md` - Removed class example, added functional guidance

### Added
- **Atomic Knowledge Base Structure**:
  - `principles/atomic-knowledge-base.md` - Core principles documentation
  - 11 naming convention notes in `naming/`
  - 9 testing practice notes in `testing/`
  - 9 architecture pattern notes in `architecture/`
  - 7 git workflow notes in `git-workflow/`
  - Index files for each topic directory
- **Improved Discovery**:
  - Topic-based directories with clear organization
  - Cross-referenced notes with "Related Notes" sections
  - Searchable by topic, keyword, or file pattern
- **LLM Optimization**:
  - Updated CLAUDE.md with atomic knowledge base usage patterns
  - Updated README.md with quick start guides for humans and LLMs
  - Each note self-contained and independently useful

### Removed
- **TypeScript References**: Removed all TypeScript-specific content
  - Deleted `naming/types-pascalcase.md` (TypeScript-specific)
  - Removed `.tsx` and `.ts` file extension references
  - Updated all examples to use plain JavaScript
  - Refocused `enums-pattern.md` on JavaScript objects instead of TypeScript enums
- **Comprehensive Standards Documents**:
  - `standards/code-style.md` (replaced by 11 atomic notes in naming/)
  - `standards/testing-practices.md` (replaced by 9 atomic notes in testing/)
  - `standards/architecture-patterns.md` (replaced by 9 atomic notes in architecture/)
  - `standards/development-workflows.md` (replaced by 7 atomic notes in git-workflow/)
  - `standards/` directory itself

### Migration Notes

**For LLM Tools**:
- Instead of loading entire comprehensive documents, now load specific atomic notes
- Start with topic index files (e.g., `naming/index.md`)
- Follow cross-reference links as needed
- Expect 80-90% reduction in token usage for most tasks

**For Projects Referencing This Repo**:
- Update CLAUDE.md references from `standards/*.md` to new atomic note paths
- Use topic index files as entry points
- Link to specific atomic notes when documenting patterns

---

## Version History

### 2025-11-18 - Atomic Knowledge Base Restructure
- Converted from comprehensive documents to atomic knowledge base
- Created 36+ focused, single-concept notes
- Organized into 4 topic directories
- Optimized for LLM consumption

### 2025-11-18 - Initial Release
- Created engineering standards repository
- Established baseline standards across all categories
