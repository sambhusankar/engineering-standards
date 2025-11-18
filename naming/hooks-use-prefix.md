# React Hooks: use Prefix

Custom React hooks must start with `use` followed by a descriptive name in `camelCase`.

## Pattern

```javascript
function useAuth() { }
function useFetchData(url) { }
function useLocalStorage(key, initialValue) { }
function useDebounce(value, delay) { }
```

## Why the `use` Prefix

React enforces Rules of Hooks. The `use` prefix signals to React's linter that this function follows hook rules:
- Only call at top level
- Only call from React functions

## Examples

```javascript
function useAuth() {
  const [user, setUser] = useState(null);
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    checkAuthStatus().then(setUser).finally(() => setIsLoading(false));
  }, []);

  return { user, isLoading };
}

function useWindowSize() {
  const [size, setSize] = useState({ width: window.innerWidth, height: window.innerHeight });

  useEffect(() => {
    const handleResize = () => setSize({ width: window.innerWidth, height: window.innerHeight });
    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return size;
}
```

## Related Notes
- [Custom Hooks Pattern](../architecture/custom-hooks.md)
- [Functions: camelCase](./functions-camelcase.md)
- [Components: PascalCase](./components-pascalcase.md)
