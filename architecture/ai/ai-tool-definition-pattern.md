# AI Tool Definition Pattern

Define AI tools as generator functions that return tool objects with Zod schemas.

## Pattern

```javascript
import "server-only";
import { tool } from "ai";
import { z } from 'zod';

export function generateGetMetrics({ cookieStore, context_id }) {
  return tool({
    description: `Get metrics for resources.
    Timestamps should be ISO 8601 format with timezone.`,

    inputSchema: z.object({
      metric: z.enum(['metric_a', 'metric_b']),
      start: z.string().datetime({ offset: true }),
      end: z.string().datetime({ offset: true }),
      resource_ids: z.array(z.string()).optional(),
    }),

    execute: async (input) => {
      const { metric, start, end, resource_ids } = input;
      const data = await dataSVC.getMetrics({
        cookieStore,
        context: context_id,
        start, end,
        resources: resource_ids,
      });
      return { metric, result: data };
    },
  });
}
```

## Key Design Decisions

1. **Generator Pattern**: Functions return tool definitions, capturing context in closure
2. **Zod Schema**: Validates parameters before execution
3. **`input` Parameter**: Accept as single object, destructure inside (see [Input Pattern](/architecture/ai/ai-tool-input-pattern.md))

## Tool Registration

```javascript
const tools = {
  getMetrics: generateGetMetrics({ cookieStore, context_id }),
  getTimeline: generateGetTimeline({ cookieStore, context_id }),
};

const result = streamText({
  model: openai("gpt-4.1"),
  tools,
});
```

## Related Notes
- [AI Tool Server-Only](/architecture/ai/ai-tool-server-only.md)
- [AI Tool Error Handling](/architecture/ai/ai-tool-error-handling.md)
- [AI Tool Input Pattern](/architecture/ai/ai-tool-input-pattern.md)
- [AI Tool Service Abstraction](/architecture/ai/ai-tool-service-abstraction.md)
- [AI Tool File Organization](/architecture/ai/ai-tool-file-organization.md)
- [AI Tool Testing](/architecture/ai/ai-tool-testing.md)
