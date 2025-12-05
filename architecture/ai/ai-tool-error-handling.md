# AI Tool Error Handling Pattern

Use centralized `handleToolError` utility for consistent Sentry logging across all tools.

## Error Handler Utility

```javascript
// src/tools/utils/handleToolError.js
import * as Sentry from "@sentry/nextjs";

export function handleToolError(error, context) {
  const { tool_name, tool_input, plant_id, device_id } = context;

  error.name = "ToolError";

  Sentry.captureException(error, {
    extra: { tool_name, tool_input, plant_id, device_id },
    tags: {
      tool: tool_name,
      plant_id,
      ...(device_id && { device_id }),
    }
  });

  // Throw plain object for AI SDK serialization
  throw { message: error.message, stack: error.stack };
}
```

## Usage in Tools

```javascript
import { handleToolError } from "./utils/handleToolError";

execute: async (input) => {
  try {
    const data = await vimanaSVC.getMetrics({ ... });
    return { result: data };
  } catch (error) {
    handleToolError(error, {
      tool_name: "getMetrics",
      tool_input: input,  // Full input for debugging
      plant_id: p_id,
    });
  }
}
```

## Benefits

- Consistent Sentry tagging (filter by `tool: "getMetrics"`)
- Full input captured for reproduction
- Stack trace preserved for debugging

## Graceful Degradation (Optional)

For non-critical tools, return error instead of throwing:

```javascript
catch (error) {
  Sentry.captureException(error, { ... });
  return { status: 'error', error: error.message, data: [] };
}
```

## Related Notes
- [AI Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md)
- [AI Tool Input Pattern](/architecture/ai/ai-tool-input-pattern.md)
- [AI Tool Testing](/architecture/ai/ai-tool-testing.md)
