# AI SDK Client Hooks Integration

Use `useChat()` hook from Vercel AI SDK to manage chat state and messaging on the client.

## Basic Setup

```javascript
"use client";

import { useChat } from "@ai-sdk/react";
import { DefaultChatTransport } from "ai";
import { useState, useMemo } from "react";

export default function ChatBot({ chat, initialMessages = [] }) {
  const [input, setInput] = useState('');
  const [llmModel, setLlmModel] = useState("gpt-4.1");
  const { p_id, t_id } = useParams();

  // Generate or use existing chat ID (memoized)
  const chatId = useMemo(() => chat.id || generateUUID(), [chat.id]);

  // Transform database messages to AI SDK format
  const transformedMessages = initialMessages.map(msg => ({
    id: msg.id,
    role: msg.role,
    parts: msg.parts || [],  // AI SDK v5 uses parts array
    createdAt: msg.created_at
  }));

  // Initialize useChat hook
  const {
    status,
    messages,
    stop,
    sendMessage,
    setMessages,
    error
  } = useChat({
    id: chatId,
    // Custom transport for dynamic parameters
    transport: new DefaultChatTransport({
      body: () => ({
        p_id,
        t_id,
        llmModel  // Pass dynamic values at send time
      })
    }),
    initialMessages: transformedMessages,
    onError: (error) => {
      console.error("Chat error:", error);
      handleChatError(error);
    }
  });

  // Workaround: useChat doesn't load initialMessages reliably
  // Manually set if empty but we have initialMessages
  React.useEffect(() => {
    if (messages.length === 0 && transformedMessages.length > 0) {
      setMessages(transformedMessages);
    }
  }, [messages.length, transformedMessages, setMessages]);

  // Custom submit handler (AI SDK v5 doesn't use form)
  const handleSubmit = () => {
    if (input.trim()) {
      sendMessage({ text: input });
      setInput('');
    }
  };

  return (
    <div>
      {/* Render messages */}
      {messages.map(msg => (
        <Message key={msg.id} message={msg} />
      ))}

      {/* Input area */}
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        onKeyPress={(e) => e.key === 'Enter' && handleSubmit()}
        placeholder="Type a message..."
      />
      <button onClick={handleSubmit} disabled={status === 'pending'}>
        Send
      </button>
    </div>
  );
}
```

## Message Rendering Pattern

```javascript
// Handle different message roles and types
function Message({ message }) {
  if (message.role === 'user') {
    return <UserMessage parts={message.parts} />;
  }

  if (message.role === 'assistant') {
    return <AssistantMessage parts={message.parts} />;
  }

  // Tool messages
  if (message.role === 'tool') {
    return <ToolMessage content={message.content} />;
  }

  return null;
}

// User message - extract text from parts
function UserMessage({ parts }) {
  const text = parts?.find(p => p.type === 'text')?.text || '';
  return <div className="user-message">{text}</div>;
}

// Assistant message - handle text and tool calls
function AssistantMessage({ parts }) {
  return (
    <div className="assistant-message">
      {parts?.map((part, idx) => {
        if (part.type === 'text') {
          return <p key={idx}>{part.text}</p>;
        }
        if (part.type === 'tool-call') {
          return <ToolCall key={idx} call={part} />;
        }
        return null;
      })}
    </div>
  );
}

// Tool message - display tool results or errors
function ToolMessage({ content }) {
  if (typeof content === 'string') {
    return <div className="tool-message">{content}</div>;
  }
  return <pre>{JSON.stringify(content, null, 2)}</pre>;
}
```

## Related Notes
- [AI SDK Server Integration](/architecture/ai/ai-sdk-server-streaming.md) - Server-side streaming
- [Message Format Normalization](/architecture/ai/message-format-normalization.md) - Message handling
- [Tool Call Structure](/architecture/ai/tool-call-structure.md) - Displaying tool calls
- [AI SDK Error Recovery](/architecture/ai/ai-sdk-error-recovery.md) - Error handling and recovery
- [AI SDK Dynamic Model Selection](/architecture/ai/ai-sdk-dynamic-model-selection.md) - Model switching
- [AI SDK Chat Persistence](/architecture/ai/ai-sdk-chat-persistence.md) - LocalStorage management
