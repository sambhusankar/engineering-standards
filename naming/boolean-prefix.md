# Boolean Variables: Prefix Convention

Prefix boolean variable names with `is`, `has`, `should`, or `can` to make their type and purpose clear.

## Prefixes

**`is`** - State or condition:
```javascript
const isActive = true;
const isLoading = false;
const isValid = checkValidity();
```

**`has`** - Possession or presence:
```javascript
const hasPermission = user.role === 'admin';
const hasError = errors.length > 0;
const hasChildren = node.children.length > 0;
```

**`should`** - Conditional behavior:
```javascript
const shouldRetry = attemptCount < MAX_RETRIES;
const shouldRedirect = !user.isAuthenticated;
```

**`can`** - Capability or permission:
```javascript
const canEdit = user.role === 'admin' || user.id === item.ownerId;
const canDelete = hasPermission('delete');
```

## Why This Matters

Clear boolean naming makes conditional logic self-documenting:

```javascript
// Clear
if (isActive && hasPermission) { }

// Unclear
if (active && permission) { }
```

## Related Notes
- [Variables: camelCase](./variables-camelcase.md)
- [Functions: camelCase](./functions-camelcase.md)
