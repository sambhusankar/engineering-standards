# Variables: snake_case

Use `snake_case` for variable names in JavaScript.

## Pattern

All lowercase with underscores separating words:

```javascript
const user_name = "Alex";
const max_retry_count = 3;
let is_loading = false;
const api_base_url = "https://api.example.com";
```

## When to Use

- Local variables
- Function parameters
- Object properties (data)
- Most constant values (see exception below)

## Exceptions

**React Hook Returns**: Variables returned from React hooks use `camelCase` (React ecosystem convention):

```javascript
// Hook returns: camelCase
const [isEditing, setIsEditing] = useState(false);
const { data, error, isLoading } = useQuery();
const { user, isAuthenticated, login } = useAuth();

// But derived values and local variables: snake_case
const display_name = user?.name || 'Guest';
const error_message = error?.message;
```

**Configuration Constants**: Use `SCREAMING_SNAKE_CASE`:

```javascript
const MAX_UPLOAD_SIZE = 5242880; // Configuration constant
const current_user = getCurrentUser(); // Regular variable
```

## Contrast with Functions

Functions use `camelCase`, variables use `snake_case`:

```javascript
// Functions: camelCase
function getUserData(user_id) {
  const api_response = fetchUser(user_id);
  return api_response;
}

// Variables: snake_case
const user_id = 123;
const user_data = getUserData(user_id);
```

## React Naming

**Props**: React props use `camelCase` (React community standard):

```javascript
function UserProfile({ userId, userName, isActive }) {
  // Props are camelCase: userId, userName, isActive

  // Hook returns are camelCase
  const [isEditing, setIsEditing] = useState(false);

  // Your variables are snake_case
  const display_name = userName || 'Anonymous';
  const status_class = isActive ? 'active' : 'inactive';

  return <div className={status_class}>{display_name}</div>;
}
```

**Event Handlers**: Variables inside event handlers use `snake_case`:

```javascript
function Component() {
  const handleClick = () => {
    // Variables in event handlers: snake_case
    const new_value = count + 1;
    const updated_data = { ...data, value: new_value };
    setData(updated_data);
  };

  return <button onClick={handleClick}>Click</button>;
}
```

## Related Notes
- [Boolean Variables Prefix](/naming/boolean-prefix.md)
- [Constants: SCREAMING_SNAKE_CASE](/naming/constants-screaming-snake.md)
- [Functions: camelCase](/naming/functions-camelcase.md)
