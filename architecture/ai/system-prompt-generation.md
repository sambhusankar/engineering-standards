# System Prompt Generation

Create dynamic, context-aware system prompts that gather context at request time.

## Dynamic Prompt Pattern

Generate system prompts by fetching relevant context in parallel:

```javascript
/**
 * Generate context-specific system prompt
 * Fetches context data and builds prompt dynamically
 */
export async function generateSystemPrompt(contextId, context) {
  // Fetch context data in parallel
  const [baseInfo, resources, capabilities] = await Promise.all([
    fetchBaseContext(contextId),
    fetchResources(contextId),
    fetchCapabilities(contextId)
  ]);

  // Build prompt from context
  const systemPrompt = `You are an assistant for ${baseInfo.name}.

## Context
- Current Time: ${baseInfo.currentTime}
- Available Resources: ${resources.length}
- Available Capabilities: ${capabilities.map(c => c.name).join(', ')}

## Your Role
Provide helpful, accurate assistance using available resources and capabilities.`;

  return systemPrompt;
}
```

## Related Notes
- [AI SDK Server Integration](./ai-sdk-server-streaming.md) - Where prompts are used
- [System Prompt Optimization](./system-prompt-optimization.md) - Templates and performance
