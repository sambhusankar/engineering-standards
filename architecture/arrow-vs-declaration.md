# Arrow Functions vs Function Declarations

Choose between arrow functions and function declarations based on context and purpose.

## Guidelines

**Use arrow functions for:**
- Callbacks and inline functions
- Short, simple functions
- Preserving `this` context

**Use function declarations for:**
- Top-level named functions
- Functions with complex logic
- Functions that need hoisting

## Arrow Functions

```javascript
// Callbacks
const users = data.map(user => user.name);
const filtered = items.filter(item => item.active);

// Simple operations
const add = (a, b) => a + b;
const double = x => x * 2;

// Event handlers in React
function Component() {
  const handleClick = () => {
    console.log('clicked');
  };

  return <button onClick={handleClick}>Click</button>;
}
```

## Function Declarations

```javascript
// Top-level named functions
function processUserData(data) {
  // Complex logic with multiple steps
  const validated = validateData(data);
  const transformed = transformData(validated);
  return enrichData(transformed);
}

// Functions that need hoisting
function main() {
  helper(); // Can call before declaration
}

function helper() {
  console.log('helper');
}

// Named functions for stack traces
function calculateComplexValue() {
  // Error stack trace will show function name
}
```

## React Components

Both work, but arrow functions common for simple components:

```javascript
// Arrow function (common)
const Button = ({ children, onClick }) => {
  return <button onClick={onClick}>{children}</button>;
};

// Function declaration (also fine)
function Button({ children, onClick }) {
  return <button onClick={onClick}>{children}</button>;
}
```

## `this` Context

Arrow functions don't have their own `this`:

```javascript
class DataService {
  constructor() {
    this.data = [];
  }

  // Arrow function preserves this
  fetchData = async () => {
    const result = await fetch('/api/data');
    this.data = await result.json();  // `this` refers to instance
  };

  // Regular function needs binding
  fetchDataRegular() {
    fetch('/api/data')
      .then(result => result.json())
      .then(data => {
        this.data = data;  // Need arrow function in callback
      });
  }
}
```

## Related Notes
- [Functions: camelCase](../naming/functions-camelcase.md)
- [Async/Await Pattern](./async-await-pattern.md)
- [Component Patterns](./container-presentational-pattern.md)
