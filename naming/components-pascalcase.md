# React Components: PascalCase

Use `PascalCase` for React component names - capitalize the first letter of each word.

## Pattern

```javascript
function UserProfile() { }
const ChatMessage = () => { };
function NavigationBar() { }
```

## File Naming

Component files should also use `PascalCase`:

```
UserProfile.jsx
ChatMessage.jsx
NavigationBar.jsx
```

## Why PascalCase

React requires component names to start with capital letter to distinguish them from HTML elements:

```javascript
// Component - capitalized
<UserProfile />

// HTML element - lowercase
<div />
```

## Multi-Word Components

Capitalize each word without separators:

```javascript
// Correct
function UserProfileCard() { }
function ShoppingCartItem() { }
function EmailVerificationForm() { }

// Wrong
function userProfileCard() { }
function Shopping_Cart_Item() { }
```

## Related Notes
- [Component Files](./files-components.md)
- [Hooks: use Prefix](./hooks-use-prefix.md)
- [Custom Hooks Pattern](../architecture/components/custom-hooks.md)
