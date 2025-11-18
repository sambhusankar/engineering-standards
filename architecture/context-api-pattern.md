# Context API for Global State

Use React Context API for sharing state across many components without prop drilling.

## Pattern

```javascript
// Create context
const AuthContext = createContext();

// Provider component
export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const value = {
    user,
    login: async (credentials) => {
      const authenticated_user = await authenticate(credentials);
      setUser(authenticated_user);
    },
    logout: () => setUser(null),
  };

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}

// Custom hook for easy access
export function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error('useAuth must be used within AuthProvider');
  }
  return context;
}
```

## Usage

```javascript
// Wrap app with provider
function App() {
  return (
    <AuthProvider>
      <Dashboard />
    </AuthProvider>
  );
}

// Use in any nested component
function UserMenu() {
  const { user, logout } = useAuth();

  return (
    <div>
      <span>{user.name}</span>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

## When to Use Context

**Use Context for:**
- User authentication
- Application theme
- Language/localization
- Shared data accessed by many components

**Don't use Context for:**
- Data used by only 2-3 components (use props)
- Frequently changing data (consider state management library)
- Server state (use React Query/SWR)

## Performance Consideration

Memoize context value to prevent unnecessary re-renders:

```javascript
export function AuthProvider({ children }) {
  const [user, setUser] = useState(null);

  const value = useMemo(
    () => ({
      user,
      login: async (credentials) => {
        const authenticated_user = await authenticate(credentials);
        setUser(authenticated_user);
      },
      logout: () => setUser(null),
    }),
    [user]
  );

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}
```

## Related Notes
- [State Management: Local vs Global](./state-management-local-vs-global.md)
- [Custom Hooks Pattern](./custom-hooks.md)
- [State Colocation](./state-colocation.md)
