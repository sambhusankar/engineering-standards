# Custom Hooks Pattern

Extract reusable stateful logic into custom hooks.

## Pattern

Custom hooks encapsulate state and side effects:

```javascript
function useAuth() {
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    checkAuthStatus()
      .then(setUser)
      .finally(() => setIsLoading(false));
  }, []);

  const login = async (credentials) => {
    const user = await authenticate(credentials);
    setUser(user);
  };

  const logout = () => {
    setUser(null);
    clearSession();
  };

  return { user, isLoading, login, logout };
}
```

## Usage

```javascript
function App() {
  const { user, isLoading, login, logout } = useAuth();

  if (isLoading) return <LoadingSpinner />;

  return user ? (
    <Dashboard user={user} onLogout={logout} />
  ) : (
    <LoginForm onLogin={login} />
  );
}
```

## Common Custom Hooks

**Data fetching:**
```javascript
function useFetchData(url) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setIsLoading(false));
  }, [url]);

  return { data, error, isLoading };
}
```

**Local storage:**
```javascript
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}
```

## Benefits

- **Reusability**: Use same logic across components
- **Testability**: Test hooks in isolation
- **Organization**: Keep components focused on rendering

## Related Notes
- [Hooks Naming: use Prefix](../naming/hooks-use-prefix.md)
- [Container/Presentational Pattern](./container-presentational-pattern.md)
- [State Colocation](./state-colocation.md)
