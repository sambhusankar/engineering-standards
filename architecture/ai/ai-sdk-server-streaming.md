# AI SDK Server Streaming Integration

Implement server-side streaming chat responses using `streamText()` with tool calling support.

## Core Pattern

```javascript
import { openai } from "@ai-sdk/openai";
import { streamText, validateUIMessages, convertToModelMessages } from "ai";
import { cookies } from "next/headers";

export async function POST(req) {
  const { messages, context_id, llmModel } = await req.json();
  const cookieStore = await cookies();

  // 1. Normalize messages to UIMessage format
  const normalizedMessages = messages.map(msg => {
    if (msg.content !== undefined) {
      return {
        id: msg.id,
        role: msg.role,
        parts: [{ type: 'text', text: msg.content }],
        createdAt: msg.createdAt
      };
    }
    return msg;
  });

  // 2. Validate messages against tools
  const tools = {
    getMetrics: generateGetMetrics({ cookieStore, context_id }),
    getTimeline: generateGetTimeline({ cookieStore, context_id }),
  };

  const validatedMessages = await validateUIMessages({
    messages: normalizedMessages,
    tools
  });

  // 3. Stream response with tool support
  const result = streamText({
    model: openai(llmModel || "gpt-4.1"),
    system: systemPrompt,
    messages: convertToModelMessages(validatedMessages),
    tools: tools,
    stopWhen: stepCountIs(5),  // Limit tool calls to prevent infinite loops

    // 4. Handle completion - persistence, analytics, etc.
    onFinish: async (event) => {
      // Process tool calls and responses from event.steps
      if (event.steps && event.steps.length > 0) {
        const toolCalls = extractToolCalls(event.steps);
        await trackEvent('chat_completed', { toolCalls });
      }
    }
  });

  return result.toDataStreamResponse();
}

// Handle streaming responses up to 30 seconds
export const maxDuration = 30;
```

## Key Configuration Options

```javascript
const result = streamText({
  // Model selection
  model: openai("gpt-4.1"),        // or "gpt-4.1-nano" for lower cost

  // System instruction
  system: systemPrompt,

  // Message history (converted to provider format)
  messages: convertToModelMessages(validatedMessages),

  // Available tools for AI
  tools: {
    getMetrics: generateGetMetrics({ cookieStore, context_id }),
    getTimeline: generateGetTimeline({ cookieStore, context_id }),
  },

  // Prevent infinite tool call loops
  stopWhen: stepCountIs(5),

  // Lifecycle hook for cleanup/persistence
  onFinish: async (event) => {
    // event.text = final text response
    // event.steps = array of tool calls/responses
    // event.usage = token counts
  },

  // Temperature for response creativity
  temperature: 0.7,

  // Maximum tokens for single response
  maxTokens: 2000,
});
```

## Message Validation Pattern

```javascript
// Validate messages before passing to AI
const validatedMessages = await validateUIMessages({
  messages: normalizedMessages,
  tools
});

// This cleans provider metadata and ensures proper structure
// Essential before convertToModelMessages()
```

## Tool Call Extraction from Steps

```javascript
function extractToolCalls(steps) {
  const toolCalls = [];

  steps.forEach(step => {
    if (!step.content || !Array.isArray(step.content)) return;

    step.content.forEach(item => {
      // Tool call: AI requested tool execution
      if (item.type === 'tool-call') {
        toolCalls.push({
          name: item.toolName,
          args: item.args,
          id: item.toolCallId
        });
      }

      // Tool result: Response from tool execution
      if (item.type === 'tool-result') {
        toolCalls.push({
          type: 'result',
          toolName: item.toolName,
          result: item.result,
          toolCallId: item.toolCallId
        });
      }

      // Tool error: Error from tool execution
      if (item.type === 'tool-error') {
        toolCalls.push({
          type: 'error',
          toolName: item.toolName,
          error: item.error,
          toolCallId: item.toolCallId
        });
      }
    });
  });

  return toolCalls;
}
```

## Handling Different Chat Types

For specialized chats with different scopes, adjust context:

```javascript
// Scope-specific chat with fewer tools
export async function POST(req) {
  const { messages, context_id, scope_id, llmModel } = await req.json();

  // Generate scope-specific system prompt
  const systemPrompt = await generateScopedSystemPrompt(context_id, scope_id, cookieStore);

  // Scoped tools only
  const tools = {
    getMetrics: generateGetMetrics({ cookieStore, context_id, scope_id }),
    getScopedTimeline: generateGetScopedTimeline({ cookieStore, context_id, scope_id }),
    getScopedData: generateGetScopedData({ cookieStore, context_id, scope_id }),
  };

  return streamText({
    model: openai(llmModel || "gpt-4.1-nano"),
    system: systemPrompt,
    messages: convertToModelMessages(validatedMessages),
    tools,
    stopWhen: stepCountIs(5),
  }).toDataStreamResponse();
}
```

## Error Handling Strategy

```javascript
export async function POST(req) {
  try {
    const { messages, context_id } = await req.json();

    if (!messages || !Array.isArray(messages)) {
      return new Response("Invalid messages format", { status: 400 });
    }

    // ... tool setup, validation ...

    const result = streamText({
      // ... config ...
      onFinish: async (event) => {
        try {
          // Persist chat and analytics
          await persistChat(event);
        } catch (persistError) {
          // Log but don't fail the response
          console.error('Error persisting chat:', persistError);
        }
      }
    });

    return result.toDataStreamResponse();
  } catch (error) {
    console.error('Chat API error:', error);
    return new Response(JSON.stringify({ error: error.message }), {
      status: 500,
      headers: { 'Content-Type': 'application/json' }
    });
  }
}
```

## Performance Considerations

- **Model Selection**: Use `gpt-4.1-nano` for faster, cheaper responses when possible
- **Tool Count**: More tools increase response latency; limit to essential tools
- **Step Limit**: `stepCountIs(5)` prevents excessive tool call loops
- **Streaming**: Stream responses immediately, don't wait for all tools to complete
- **Timeout**: Set `maxDuration` appropriately (30s for complex operations)

## Related Notes
- [Message Format Normalization](/architecture/ai/message-format-normalization.md) - Message conversion patterns
- [Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md) - Creating tools
- [Tool Call Structure](/architecture/ai/tool-call-structure.md) - Processing tool responses
- [AI SDK Client Integration](/architecture/ai/ai-sdk-client-hooks.md) - Client-side consumption
