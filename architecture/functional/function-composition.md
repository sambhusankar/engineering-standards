# Function Composition

Build complex behavior by composing small, focused functions together.

## Pattern: Small Composable Functions

```javascript
// Small, focused functions
const add = (a, b) => a + b;
const multiply = (a, b) => a * b;
const subtract = (a, b) => a - b;

// Compose them
function calculateDiscount(price, discount_percent) {
  const discount = multiply(price, discount_percent / 100);
  return subtract(price, discount);
}

console.log(calculateDiscount(100, 10)); // 90
```

## Pipe Pattern

Chain functions where output of one becomes input of next:

```javascript
const pipe = (...fns) => (value) => fns.reduce((acc, fn) => fn(acc), value);

const processPrice = pipe(
  (price) => price * 0.9,                   // 10% discount
  (price) => price * 1.08,                  // Add 8% tax
  (price) => Math.round(price * 100) / 100  // Round to 2 decimals
);

console.log(processPrice(100)); // 97.2
```

## Compose Pattern

Like pipe, but applies functions right-to-left:

```javascript
const compose = (...fns) => (value) =>
  fns.reduceRight((acc, fn) => fn(acc), value);

const addTax = (price) => price * 1.08;
const applyDiscount = (price) => price * 0.9;
const round = (price) => Math.round(price * 100) / 100;

const processPrice = compose(round, addTax, applyDiscount);

console.log(processPrice(100)); // 97.2
```

## Higher-Order Functions

Functions that take or return other functions:

```javascript
// Function that returns a function
function createMultiplier(factor) {
  return (number) => number * factor;
}

const double = createMultiplier(2);
const triple = createMultiplier(3);

console.log(double(5));  // 10
console.log(triple(5));  // 15

// Function that takes a function
function applyOperation(numbers, operation) {
  return numbers.map(operation);
}

const numbers = [1, 2, 3, 4];
applyOperation(numbers, double);  // [2, 4, 6, 8]
applyOperation(numbers, triple); // [3, 6, 9, 12]
```

## Practical Example: Data Processing

```javascript
const users = [
  { id: 1, name: 'alice', age: 30, active: true },
  { id: 2, name: 'bob', age: 25, active: false },
  { id: 3, name: 'charlie', age: 35, active: true }
];

// Small functions
const isActive = (user) => user.active;
const capitalize = (str) => str.charAt(0).toUpperCase() + str.slice(1);
const getCapitalizedName = (user) => ({ ...user, name: capitalize(user.name) });

// Compose them
const processUsers = (users) => users
  .filter(isActive)
  .map(getCapitalizedName)
  .map(user => user.name);

console.log(processUsers(users)); // ['Alice', 'Charlie']
```

## Currying

Transform a function with multiple arguments into a sequence of functions:

```javascript
// Regular function
function add(a, b, c) {
  return a + b + c;
}

// Curried version
const curriedAdd = (a) => (b) => (c) => a + b + c;

console.log(curriedAdd(1)(2)(3)); // 6

// Partial application
const add5 = curriedAdd(5);
const add5and10 = add5(10);
console.log(add5and10(3)); // 18
```

## Benefits

- **Reusability** - Small functions used in many combinations
- **Testability** - Each function tested independently
- **Readability** - Self-documenting data flows
- **Flexibility** - Easy to add/remove steps in pipeline

## Related Notes
- [Pure Functions](/architecture/functional/pure-functions.md)
- [Array Functional Methods](/architecture/functional/array-methods-functional.md)
- [Functional Programming](/principles/functional-programming.md)
