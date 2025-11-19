# Boolean Variables: Prefix Convention

Prefix boolean variable names with `is`, `has`, `should`, or `can` to make their type and purpose clear.

## Prefixes

**`is`** - State or condition:
```javascript
const is_active = true;
const is_loading = false;
const is_valid = checkValidity();
```

**`has`** - Possession or presence:
```javascript
const has_permission = user.role === 'admin';
const has_error = errors.length > 0;
const has_children = node.children.length > 0;
```

**`should`** - Conditional behavior:
```javascript
const should_retry = attempt_count < MAX_RETRIES;
const should_redirect = !user.is_authenticated;
```

**`can`** - Capability or permission:
```javascript
const can_edit = user.role === 'admin' || user.id === item.owner_id;
const can_delete = hasPermission('delete');
```

## Why This Matters

Clear boolean naming makes conditional logic self-documenting:

```javascript
// Clear
if (is_active && has_permission) { }

// Unclear
if (active && permission) { }
```

## Related Notes
- [Variables: snake_case](/naming/variables-snake-case.md)
- [Functions: camelCase](/naming/functions-camelcase.md)
