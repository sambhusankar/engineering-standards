# JSDoc: Discriminated Union Types

Use union types with literal string discriminators when an array or object can hold multiple different shapes.

## The Problem

TypeScript infers array element types from the first element:

```javascript
// @ts-check

// TypeScript infers: values must be { gte: Date, lt: Date }
const filters = [
  {
    filterType: "range",
    fieldName: "timestamp",
    values: { gte: startDate, lt: endDate }
  }
];

// ERROR: Type 'string[]' not assignable to '{ gte: Date, lt: Date }'
filters.push({
  filterType: "terms",
  fieldName: "device",
  values: ["device-1", "device-2"]  // Different shape!
});
```

## The Solution: Discriminated Unions

Define separate types with literal `filterType` values, then combine with union:

```javascript
// @ts-check

/**
 * @typedef {Object} RangeFilter
 * @property {'range'} filterType
 * @property {string} fieldName
 * @property {{ gte: Date, lt: Date }} values
 */

/**
 * @typedef {Object} TermsFilter
 * @property {'terms'} filterType
 * @property {string} fieldName
 * @property {string[]} values
 */

/** @typedef {RangeFilter | TermsFilter} Filter */

// Now annotate the array
/** @type {Filter[]} */
const filters = [
  {
    filterType: "range",
    fieldName: "timestamp",
    values: { gte: startDate, lt: endDate }
  }
];

// Works! TypeScript knows filters can hold either shape
filters.push({
  filterType: "terms",
  fieldName: "device",
  values: ["device-1", "device-2"]
});
```

## Key Pattern

1. **Define each variant** with a literal string discriminator (`'range'`, `'terms'`)
2. **Combine with union** using `|` operator
3. **Annotate the container** with `@type`

## When to Use

- Arrays that hold objects of different shapes
- API request/response bodies with multiple formats
- Event objects with different payloads
- Filter systems with various filter types

## Related Notes
- [JSDoc Type Definitions](/naming/jsdoc/jsdoc-typedef.md)
- [JSDoc Gradual Type Checking](/naming/jsdoc/jsdoc-gradual-typecheck.md)
- [JSDoc Basic Annotations](/naming/jsdoc/jsdoc-basic-annotations.md)
