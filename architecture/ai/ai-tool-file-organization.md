# AI Tool File Organization

One tool per file, co-located tests, centralized exports.

## Directory Structure

```
src/tools/
├── index.js              # Central exports
├── utils/
│   └── handleToolError.js
├── getMetrics.js
├── getMetrics.test.js    # Co-located test
├── getDevices.js
├── getDevices.test.js
└── getTimeline.js
```

## Central Exports

```javascript
// src/tools/index.js
export { generateGetMetrics } from './getMetrics';
export { generateGetDevices } from './getDevices';
export { generateGetTimeline } from './getTimeline';
```

## Usage

```javascript
import { generateGetMetrics, generateGetDevices } from '@/tools';

const tools = {
  getMetrics: generateGetMetrics({ cookieStore, p_id }),
  getDevices: generateGetDevices({ cookieStore, p_id }),
};
```

## Naming Conventions

| Type | Format | Example |
|------|--------|---------|
| Tool file | `{verb}{Noun}.js` | `getMetrics.js` |
| Generator | `generate{Verb}{Noun}` | `generateGetMetrics` |
| Test file | `{toolName}.test.js` | `getMetrics.test.js` |

## New Tool Checklist

1. Create `{toolName}.js` in `src/tools/`
2. Create `{toolName}.test.js` co-located
3. Export with `generate` prefix
4. Add export to `src/tools/index.js`
5. Use shared `handleToolError` utility

## Related Notes
- [AI Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md)
- [AI Tool Testing](/architecture/ai/ai-tool-testing.md)
- [AI Tool Error Handling](/architecture/ai/ai-tool-error-handling.md)
