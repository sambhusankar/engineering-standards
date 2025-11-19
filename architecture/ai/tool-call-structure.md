# Tool Call Structure

Understand and handle tool invocations, results, and errors in AI streaming responses.

## Tool Call Flow

```
User Message
    ↓
AI Model decides to call tool
    ↓
Tool Call (with parameters)
    ↓
Tool Execution
    ↓
Tool Result/Error
    ↓
AI Model generates response using result
    ↓
Final Response
```

## AI SDK Step Structure

The `streamText()` response contains steps with tool invocations:

```javascript
// In onFinish callback
onFinish: async (event) => {
  // event.steps is array of generation steps
  event.steps?.forEach(step => {
    step.content?.forEach(item => {
      // item.type can be: 'text', 'tool-call', 'tool-result', 'tool-error'
    });
  });
}
```

## Extracting Tool Calls

```javascript
function extractToolCalls(steps) {
  const toolCalls = [];

  steps?.forEach(step => {
    step.content?.forEach(item => {
      // Tool invocation
      if (item.type === 'tool-call') {
        toolCalls.push({
          type: 'call',
          toolName: item.toolName,
          toolCallId: item.toolCallId,
          args: item.args  // Parameters passed to tool
        });
      }

      // Tool result
      if (item.type === 'tool-result') {
        toolCalls.push({
          type: 'result',
          toolCallId: item.toolCallId,
          toolName: item.toolName,
          result: item.result  // Actual data returned
        });
      }

      // Tool error
      if (item.type === 'tool-error') {
        toolCalls.push({
          type: 'error',
          toolCallId: item.toolCallId,
          toolName: item.toolName,
          error: item.error  // Error message
        });
      }
    });
  });

  return toolCalls;
}
```

## Handling Tool Errors

```javascript
function processToolCalls(steps) {
  const calls = {
    successful: [],
    failed: []
  };

  steps?.forEach(step => {
    step.content?.forEach(item => {
      if (item.type === 'tool-call') {
        // Track the call attempt
        const callInfo = {
          name: item.toolName,
          args: item.args,
          id: item.toolCallId
        };

        // Find corresponding result or error
        const result = step.content?.find(
          i => (i.type === 'tool-result' || i.type === 'tool-error') &&
               i.toolCallId === item.toolCallId
        );

        if (result?.type === 'tool-result') {
          calls.successful.push({
            ...callInfo,
            result: result.result
          });
        } else if (result?.type === 'tool-error') {
          calls.failed.push({
            ...callInfo,
            error: result.error
          });
        }
      }
    });
  });

  return calls;
}
```

## Tool Call Validation

```javascript
// Before tools execute, they're validated
const result = streamText({
  model: openai("gpt-4.1"),
  tools: {
    getMetrics: tool({
      description: "Get performance metrics",
      inputSchema: z.object({
        metric: z.enum(['performance', 'availability']),
        start: z.string().datetime({ offset: true }),
        end: z.string().datetime({ offset: true }),
      }),
      execute: async (params) => {
        // Params automatically validated against schema
        // Bad parameters never reach here
      }
    })
  },
  // Prevent infinite loops
  stopWhen: stepCountIs(5),  // Max 5 tool calls
});
```

## Rendering Tool Calls in UI

```javascript
// Display tool calls as they execute
function ToolCall({ toolCall }) {
  return (
    <div className="tool-call-container">
      <div className="tool-header">
        <span className="tool-icon">🔧</span>
        <span className="tool-name">{toolCall.toolName}</span>
        <span className="tool-status">Calling...</span>
      </div>

      <div className="tool-args">
        <pre>{JSON.stringify(toolCall.args, null, 2)}</pre>
      </div>
    </div>
  );
}

// Display tool results
function ToolResult({ result }) {
  return (
    <div className="tool-result-container">
      <div className="result-header">
        <span className="result-icon">✓</span>
        <span>Result</span>
      </div>

      <div className="result-data">
        <pre>{JSON.stringify(result, null, 2)}</pre>
      </div>
    </div>
  );
}

// Display tool errors
function ToolError({ error }) {
  return (
    <div className="tool-error-container">
      <div className="error-header">
        <span className="error-icon">❌</span>
        <span>Error</span>
      </div>

      <div className="error-message">
        {error.message || JSON.stringify(error)}
      </div>
    </div>
  );
}
```

## Tool State in Messages

Tools are part of the message history:

```javascript
// Messages may include tool invocations
const messages = [
  {
    id: '1',
    role: 'user',
    parts: [{ type: 'text', text: 'What are the performance metrics?' }]
  },
  {
    id: '2',
    role: 'assistant',
    parts: [
      {
        type: 'tool-call',
        toolName: 'getMetrics',
        toolCallId: 'call-123',
        args: {
          metric: 'performance',
          start: '2024-01-01T00:00:00Z',
          end: '2024-01-31T23:59:59Z'
        }
      }
    ]
  },
  {
    id: '3',
    role: 'tool',
    parts: [
      {
        type: 'tool-result',
        toolName: 'getMetrics',
        toolCallId: 'call-123',
        result: {
          metric: 'performance',
          value: 0.85,
          unit: '%'
        }
      }
    ]
  },
  {
    id: '4',
    role: 'assistant',
    parts: [
      {
        type: 'text',
        text: 'Your performance metric is at 85%, which indicates good overall performance...'
      }
    ]
  }
];
```

## Tool Call Metrics for Analytics

```javascript
function analyzeToolCalls(steps) {
  const calls = extractToolCalls(steps);

  const metrics = {
    // Tool usage
    tools_called: new Set(calls.map(c => c.toolName)).size,
    tool_call_count: calls.length,
    unique_tools: [...new Set(calls.map(c => c.toolName))],

    // Success rate
    successful_calls: calls.filter(c => c.type === 'result').length,
    failed_calls: calls.filter(c => c.type === 'error').length,
    success_rate: calls.filter(c => c.type === 'result').length / calls.length,

    // Tool frequency
    tool_usage: Object.fromEntries(
      [...new Set(calls.map(c => c.toolName))].map(tool => [
        tool,
        calls.filter(c => c.toolName === tool).length
      ])
    ),
  };

  return metrics;
}
```

## Debugging Tool Issues

```javascript
function debugToolCall(toolCall, result) {
  console.log('Tool Call Debug:', {
    tool: toolCall.toolName,
    id: toolCall.toolCallId,
    args: toolCall.args,
    argCount: Object.keys(toolCall.args || {}).length,
    status: result?.type,
    result: result?.result,
    error: result?.error
  });
}

// Usage
const toolCalls = extractToolCalls(steps);
toolCalls.forEach(call => {
  if (call.type === 'call') {
    const result = toolCalls.find(
      c => c.type !== 'call' && c.toolCallId === call.toolCallId
    );
    debugToolCall(call, result);
  }
});
```

## Related Notes
- [Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md) - Creating tools
- [AI SDK Server Integration](/architecture/ai/ai-sdk-server-streaming.md) - Tool execution
- [Event Tracking Pattern](/architecture/ai/event-tracking-analytics.md) - Tracking tool usage
