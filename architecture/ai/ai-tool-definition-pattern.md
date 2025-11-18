# AI Tool Definition Pattern

Define AI tools as generator functions that return tool objects with Zod schemas and execution logic.

## Pattern Overview

Tools make AI models capable of interacting with external systems. Use a generator function pattern to create tools with contextual parameters (cookieStore, context_id, etc.).

## Tool Definition Structure

```javascript
import { tool } from "ai";
import { z } from 'zod';

/**
 * Generator function returns a tool instance
 * This pattern allows tools to capture context (cookieStore, context_id) in closure
 * @param {Object} params
 * @param {Object} params.cookieStore - Authentication cookies from Next.js
 * @param {string} params.context_id - Context/scope identifier
 * @returns {Object} Tool definition for AI SDK
 */
export function generateGetMetrics({ cookieStore, context_id }) {
  const getMetrics = tool({
    description: `Get metrics for resources.
    Time period is inclusive of start and exclusive of end.
    Timestamps should be ISO 8601 format with timezone (e.g., 2024-07-17T08:00:00+05:30).`,

    // Zod schema defines expected parameters
    inputSchema: z.object({
      metric: z.enum(['metric_a', 'metric_b', 'metric_c']),
      start: z.string().datetime({ offset: true }),
      end: z.string().datetime({ offset: true }),
      resource_ids: z.array(z.string()).optional(),
    }),

    // Execute function runs when AI calls this tool
    execute: async ({ metric, start, end, resource_ids }) => {
      // Access captured context from closure
      const data = await dataSVC.getMetrics({
        cookieStore,
        context: context_id,
        start,
        end,
        resources: resource_ids,
        metrics: [metric],
      });

      return {
        metric,
        units: getMetricUnit(metric),
        result: data,
      };
    },
  });

  return getMetrics;
}
```

## Key Design Decisions

1. **Generator Pattern**: Functions that return tool definitions, not tools directly
   - Allows capturing context (authentication, IDs) in closure
   - Each request gets fresh tool instances with correct context
   - Clean separation between tool factory and tool instance

2. **Zod Schema Validation**: Required for all tool parameters
   - AI SDK validates parameters before execution
   - Provides clear type contracts to the AI model
   - Enables proper error handling

3. **Async Execution**: Tools typically call backend services
   - Use async/await for clarity
   - Catch errors and return structured responses
   - Consider data transformation (units, formatting)

## Tool Registration Pattern

```javascript
// In chat API route
const tools = {
  getMetrics: generateGetMetrics({ cookieStore, context_id }),
  getTimeline: generateGetTimeline({ cookieStore, context_id }),
  getNotifications: generateGetNotifications({ cookieStore, context_id }),
  // ... more tools
};

// Pass to AI SDK
const result = streamText({
  model: openai("gpt-4.1"),
  system: systemPrompt,
  messages: validatedMessages,
  tools: tools,
  // ... other options
});
```

## Complex Data Retrieval Pattern

For tools that need orchestrated calls, use `async.auto()` for dependency management:

```javascript
export function generateGetTimeline({ cookieStore, context_id }) {
  const getTimeline = tool({
    // ... description and schema ...
    execute: async ({ resource_id, type, start, end }) => {
      const workflow = {
        // Fetch context metadata
        getContext: async () => {
          const db = getDB();
          return db.Contexts.findOne({ where: { id: context_id } });
        },

        // Fetch resource list
        getResources: async () => {
          return dataSVC.getResources({ cookieStore, context: context_id });
        },

        // Fetch main data, depends on context/resources
        getTimeline: async () => {
          return dataSVC.getTimeline({
            cookieStore,
            context: context_id,
            start, end,
            timelines: [type],
            ...(resource_id && { resources: [resource_id] }),
          });
        },

        // Process results using async.auto dependency graph
        processResults: async (results) => {
          // Has access to results.getContext, results.getResources, results.getTimeline
          return formatTimelineData(results.getTimeline, results.getResources);
        },
      };

      const results = await async.auto(workflow);
      return results.processResults;
    },
  });

  return getTimeline;
}
```

## Error Handling

```javascript
export function generateGetNotifications({ cookieStore, context_id }) {
  const getNotifications = tool({
    description: "Get notifications for resources",
    inputSchema: z.object({
      resource_id: z.string(),
      start: z.string().datetime({ offset: true }),
      end: z.string().datetime({ offset: true }),
    }),
    execute: async ({ resource_id, start, end }) => {
      try {
        const notifications = await dataSVC.getNotifications({
          cookieStore,
          context: context_id,
          resource: resource_id,
          start, end,
        });

        return {
          resource_id,
          notification_count: notifications.length,
          notifications,
        };
      } catch (error) {
        console.error('Error fetching notifications:', error);
        // Return graceful error response instead of throwing
        return {
          resource_id,
          error: error.message,
          notifications: [],
        };
      }
    },
  });

  return getNotifications;
}
```

## Benefits of This Pattern

- **Context Capture**: Tools have access to cookies, IDs, and request context
- **Reusability**: Same tool definition used across multiple chat instances
- **Testability**: Tools can be instantiated with mock data
- **Dependency Management**: Use async.auto() for complex workflows
- **Type Safety**: Zod schemas provide runtime validation and IDE autocomplete

## Related Notes
- [Tool Call Structure](./tool-call-structure.md) - Handling tool invocation responses
- [AI SDK Server Integration](./ai-sdk-server-streaming.md) - Using tools in streaming
- [Tool Generator Functions](./tool-generator-functions.md) - Detailed generator pattern
