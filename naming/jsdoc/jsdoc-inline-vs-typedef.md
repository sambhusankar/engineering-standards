# JSDoc: Inline Types vs @typedef

Use inline object types for single-use props; use @typedef for types shared within a file or directory.

## When to Use Inline Types

For component props used only once:

```javascript
/**
 * Floating chat bubble button that toggles the AI assistant
 *
 * @param {Object} props
 * @param {() => void} props.onClick - Function called when clicked
 * @param {boolean} props.is_open - Whether the chat window is open
 * @returns {JSX.Element}
 */
export default function ChatBubble({ onClick, is_open }) {
  return <Button onClick={onClick}>...</Button>;
}
```

**Benefits:**
- Less code for single-use types
- Everything in one place
- No need to export/import types

## When to Use @typedef

For types shared across multiple files in same directory:

```javascript
// ChatMessages.jsx
/**
 * @typedef {Object} Message
 * @property {'user' | 'assistant'} role - Message sender role
 * @property {string} content - Message content
 */

/**
 * @param {Object} props
 * @param {Message[]} props.messages - Array of chat messages
 * @returns {JSX.Element}
 */
export default function ChatMessages({ messages }) {
  // Implementation
}
```

**Benefits:**
- Reusable across multiple files
- Consistent type definition
- Self-documenting shared data structures

## Decision Criteria

**Use inline when:**
- Props are only used in one component
- Type is specific to that component
- You want minimal code overhead

**Use @typedef when:**
- Same type appears 2-3 times within same file
- Same type used across 2-5 files in same directory
- Type is moderately complex and benefits from a name

**Use centralized .types.js files when:**
- Type represents a core domain model (database entities, API types)
- Type is used across many files and directories
- See [JSDoc Centralized Types](/naming/jsdoc/jsdoc-centralized-types.md)

## Related Notes
- [JSDoc Type Definitions](/naming/jsdoc/jsdoc-typedef.md)
- [JSDoc Centralized Types](/naming/jsdoc/jsdoc-centralized-types.md)
- [JSDoc Basic Annotations](/naming/jsdoc/jsdoc-basic-annotations.md)
- [Avoid Unnecessary Abstractions](/principles/avoid-premature-abstraction.md)
