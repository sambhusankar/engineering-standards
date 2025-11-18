# Message Format Normalization

Convert between different message format standards in AI SDK clients and server.

## Message Format Standards

### Client Message Format (from useChat hook)
```javascript
{
  id: string,
  role: 'user' | 'assistant' | 'tool',
  content: string | ContentBlock[],  // AI SDK v5 style
  createdAt?: Date
}
```

### Server Message Format (UI Message)
```javascript
{
  id: string,
  role: 'user' | 'assistant' | 'tool',
  parts: [                           // Parts array format
    { type: 'text', text: string },
    { type: 'tool-call', name: string, args: object },
    { type: 'tool-result', name: string, result: object }
  ],
  createdAt?: Date
}
```

### Database Format (Persistence)
```javascript
{
  id: string,
  role: string,
  parts: JSON,                       // Stored as JSON
  attachments?: JSON,
  created_at: Date
}
```

## Normalization Pipeline

### Client → Server Normalization

Convert messages from `useChat` hook to server-compatible format:

```javascript
// When sending to server
const normalizedMessages = messages.map(msg => {
  // Client format: content field
  if (msg.content !== undefined) {
    return {
      id: msg.id,
      role: msg.role,
      parts: [{ type: 'text', text: msg.content }],
      createdAt: msg.createdAt
    };
  }
  // Already in parts format
  return msg;
});

// Send to server
const response = await fetch('/api/chat', {
  method: 'POST',
  body: JSON.stringify({
    messages: normalizedMessages,
    p_id,
    llmModel
  })
});
```

### Server → AI Model Conversion

Convert UI messages to provider-specific format:

```javascript
import { convertToModelMessages, validateUIMessages } from 'ai';

export async function POST(req) {
  const { messages, p_id } = await req.json();

  // Define tools for validation
  const tools = {
    getMetrics: generateGetMetrics({ cookieStore, p_id }),
  };

  // 1. Validate: Ensures proper structure and metadata
  const validatedMessages = await validateUIMessages({
    messages,          // UIMessage[] format
    tools             // Validates tool references
  });

  // 2. Convert: Transform to provider format (OpenAI, etc.)
  const providerMessages = convertToModelMessages(validatedMessages);

  // Now safe to pass to streamText
  const result = streamText({
    model: openai("gpt-4.1"),
    messages: providerMessages,
    // ...
  });
}
```

### Database → Client Conversion

Load persisted messages and prepare for UI:

```javascript
async function loadChatHistory(chatId) {
  const db = getDB();
  const messages = await db.Messages.findAll({
    where: { chat: chatId },
    raw: true
  });

  // Transform to client format
  const transformedMessages = messages.map(msg => ({
    id: msg.id,
    role: msg.role,
    parts: msg.parts,  // Already stored as parts array
    createdAt: msg.created_at
  }));

  return transformedMessages;
}

// In component
const initialMessages = await loadChatHistory(chatId);
const { messages } = useChat({
  initialMessages,
  // ...
});
```

## Key Conversion Points

### Message Content Extraction

```javascript
/**
 * Extract text content from message regardless of format
 * Handles both client and server message formats
 */
function extractMessageText(message) {
  // Client format: content field
  if (typeof message.content === 'string') {
    return message.content;
  }

  if (Array.isArray(message.content)) {
    const textPart = message.content.find(part =>
      part.type === 'text' || part.type === 'input_text'
    );
    return textPart?.text || '';
  }

  // Server format: parts array
  if (message.parts && Array.isArray(message.parts)) {
    const textPart = message.parts.find(part =>
      part.type === 'text'
    );
    return textPart?.text || '';
  }

  return '';
}

// Usage
const question = extractMessageText(messages[messages.length - 1]);
```

### Tool Call Extraction from Parts

```javascript
function extractToolCallsFromMessage(message) {
  if (!message.parts || !Array.isArray(message.parts)) {
    return [];
  }

  return message.parts
    .filter(part => part.type === 'tool-call')
    .map(toolCall => ({
      name: toolCall.toolName || toolCall.name,
      args: toolCall.args,
      id: toolCall.toolCallId
    }));
}
```

## Persistence Pattern

When saving messages to database:

```javascript
async function saveMessage(chatId, message) {
  const db = getDB();

  // Ensure message is in parts format before saving
  const normalizedMessage = {
    chat: chatId,
    role: message.role,
    parts: message.parts || [{ type: 'text', text: message.content }],
    attachments: message.attachments || null,
  };

  return db.Messages.create(normalizedMessage);
}

// Usage in onFinish callback
onFinish: async (event) => {
  // Save AI response
  await saveMessage(chatId, {
    role: 'assistant',
    parts: event.steps.flatMap(step => step.content || [])
  });
}
```

## Message History Management

```javascript
/**
 * Filter messages for display or processing
 * Remove tool messages, keep only user/assistant
 */
function getConversationMessages(messages) {
  return messages.filter(msg =>
    msg.role === 'user' || msg.role === 'assistant'
  );
}

/**
 * Prepare messages for streaming
 * Include all message types but exclude provider metadata
 */
function prepareMessagesForStreaming(messages) {
  return messages.filter(msg =>
    msg.role === 'user' || msg.role === 'assistant' || msg.role === 'tool'
  );
}

/**
 * Rebuild conversation from steps
 * Convert tool calls/results to proper message format
 */
function reconstructMessagesFromSteps(steps) {
  const messages = [];

  steps.forEach(step => {
    step.content?.forEach(item => {
      if (item.type === 'tool-call') {
        messages.push({
          role: 'assistant',
          parts: [{ type: 'tool-call', ...item }]
        });
      }
      if (item.type === 'tool-result') {
        messages.push({
          role: 'tool',
          parts: [{ type: 'tool-result', ...item }]
        });
      }
    });
  });

  return messages;
}
```

## Related Notes
- [AI SDK Server Integration](./ai-sdk-server-streaming.md) - Server message handling
- [AI SDK Client Hooks](./ai-sdk-client-hooks.md) - Client message management
- [Tool Call Structure](./tool-call-structure.md) - Tool-specific formats
