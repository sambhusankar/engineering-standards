# Event Tracking Chat Events

Track detailed chat interactions including tool calls, responses, and performance metrics.

## Chat Interaction Tracking

```javascript
/**
 * Track detailed chat interactions with full context
 * Captures: question, system prompt, tool calls, responses, errors
 */
export async function trackChatInteraction({
  userId,
  contextId,
  question,
  systemPrompt,
  toolCalls = [],
  toolResponses = [],
  toolErrors = [],
  aiResponse,
  llmModel,
  isInitialQuestion = false,
  steps = [],
}) {
  const eventProperties = {
    context_id: contextId,
    llm_model: llmModel,
    is_initial_question: isInitialQuestion,
    question: truncate(question, 500),
    system_prompt_length: systemPrompt?.length || 0,
    system_prompt_preview: truncate(systemPrompt, 500),

    // Tool metrics
    tool_calls_count: toolCalls.length,
    tool_calls: toolCalls.map(tc => ({
      name: tc.toolName,
      args: tc.args || {},
    })),

    tool_responses_count: toolResponses.length,
    tool_responses: toolResponses.map(tr => ({
      tool_name: tr.toolName,
      result: tr.result || {},
    })),

    tool_errors_count: toolErrors.length,
    tool_errors: toolErrors.map(te => ({
      tool_name: te.toolName,
      error: te.error || {},
    })),

    // Response metrics
    has_tool_errors: toolErrors.length > 0,
    ai_response_length: aiResponse?.length || 0,
    ai_response_preview: truncate(aiResponse, 1000),

    // Execution metrics
    steps_count: steps.length,
    timestamp: new Date().toISOString(),
  };

  await trackEvent('chat_interaction', userId, contextId, eventProperties);
}

// Helper: Truncate strings safely
const truncate = (str, maxLength = 2000) => {
  if (!str) return '';
  return str.length > maxLength ? str.substring(0, maxLength) + '...' : str;
};
```

## Integration in Chat API

```javascript
export async function POST(req) {
  const { messages, contextId, llmModel } = await req.json();
  const { user } = await authenticate(req);

  // Track analytics data
  const analyticsData = {
    userId: user.id,
    contextId,
    question: extractCurrentQuestion(messages),
    systemPrompt,
    llmModel,
    isInitialQuestion: messages.length === 1,
    toolCalls: [],
    toolResponses: [],
    toolErrors: [],
    aiResponse: '',
  };

  const result = streamText({
    // ... model config ...
    onFinish: async (event) => {
      // Extract tool calls from steps
      if (event.steps && event.steps.length > 0) {
        event.steps.forEach(step => {
          step.content?.forEach(item => {
            if (item.type === 'tool-call') {
              analyticsData.toolCalls.push({
                toolName: item.toolName,
                args: item.args
              });
            }
            if (item.type === 'tool-result') {
              analyticsData.toolResponses.push({
                toolName: item.toolName,
                result: item.result
              });
            }
            if (item.type === 'tool-error') {
              analyticsData.toolErrors.push({
                toolName: item.toolName,
                error: item.error
              });
            }
          });
        });
      }

      // Capture final response
      analyticsData.aiResponse = event.text;

      // Track the interaction
      await trackChatInteraction(analyticsData);
    }
  });
}
```

## Metrics to Track

### Basic Chat Metrics
- User ID
- Context ID (domain-specific identifier)
- Model used (gpt-4.1, gpt-4.1-nano, etc.)
- Question text (truncated for privacy)
- Is initial question (yes/no)

### Tool Usage
- Tool calls count
- Tool names called
- Tool arguments
- Tool response data
- Tool errors (if any)
- Total execution steps

### Performance
- System prompt length
- AI response length
- Total interaction duration
- Token usage (if available from API)

### Error Tracking
- Tool call failures
- Tool validation errors
- API errors
- Response generation errors

## Privacy Considerations

```javascript
// ✅ Track these
const trackableData = {
  context_id: contextId,
  tool_calls_count: toolCalls.length,
  llm_model: llmModel,
  has_tool_errors: toolErrors.length > 0,
};

// ⚠️ Truncate/summarize sensitive data
const sensitiveData = {
  question: truncate(question, 500),      // Don't store full question
  system_prompt_preview: truncate(...),   // Just a preview
};

// ❌ Never track these
// - Full PII from responses
// - Raw system prompts with sensitive config
// - Tool authentication credentials
// - Complete user messages with personal info
```

## Related Notes
- [Event Tracking Analytics](./event-tracking-analytics.md) - Dual-destination tracking pattern
- [AI SDK Server Integration](./ai-sdk-server-streaming.md) - Where tracking is called
- [Tool Call Structure](./tool-call-structure.md) - Understanding tool execution
