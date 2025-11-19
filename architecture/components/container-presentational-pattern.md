# Container/Presentational Pattern

Separate logic (container) from presentation (UI) for better maintainability and testability.

## Pattern

**Container Component** - Handles logic, state, and data fetching
**Presentational Component** - Renders UI based on props

## Example

```javascript
// Container Component (logic)
function UserProfileContainer({ userId }) {
  // Hook returns: camelCase
  const { data, error, isLoading } = useFetchUser(userId);
  const [isEditing, setIsEditing] = useState(false);

  const handleSave = async (updates) => {
    await updateUser(userId, updates);
    setIsEditing(false);
  };

  return (
    <UserProfile
      user={data}
      error={error}
      isLoading={isLoading}
      isEditing={isEditing}
      onEdit={() => setIsEditing(true)}
      onSave={handleSave}
    />
  );
}

// Presentational Component (UI)
function UserProfile({ user, error, isLoading, isEditing, onEdit, onSave }) {
  if (isLoading) return <LoadingSpinner />;
  if (error) return <ErrorMessage error={error} />;

  return (
    <div>
      <h1>{user.name}</h1>
      {isEditing ? (
        <EditForm user={user} onSave={onSave} />
      ) : (
        <button onClick={onEdit}>Edit</button>
      )}
    </div>
  );
}
```

## Benefits

- **Testability**: Presentational components easy to test (just props)
- **Reusability**: Presentational components can be reused with different data
- **Maintainability**: Clear separation of concerns
- **Performance**: Presentational components can be memoized

## When to Use

Use for:
- Complex components with API calls
- Components with significant business logic
- Reusable UI components

Don't over-apply:
- Simple components can combine both
- Small apps may not need separation

## Related Notes
- [Custom Hooks Pattern](/architecture/components/custom-hooks.md)
- [Component Composition](/architecture/components/component-composition.md)
- [State Management](/architecture/state/state-management-local-vs-global.md)
