# AI/LLM Integration Patterns

Atomic notes on implementing AI chatbot systems using Vercel AI SDK, tool definitions, streaming responses, and observability.

## AI SDK - Server Integration
- [AI SDK Server Streaming](./ai-sdk-server-streaming.md) - Server-side streaming with `streamText()`
- [AI SDK Message Validation](./message-validation.md) - *(Not yet created)* - Validating messages before streaming

## AI SDK - Client Integration
- [AI SDK Client Hooks](./ai-sdk-client-hooks.md) - Client-side `useChat()` hook setup
- [AI SDK Error Recovery](./ai-sdk-error-recovery.md) - Error handling and retry mechanisms
- [AI SDK Dynamic Model Selection](./ai-sdk-dynamic-model-selection.md) - Runtime model switching
- [AI SDK Chat Persistence](./ai-sdk-chat-persistence.md) - LocalStorage and message persistence

## Tool Management
- [Tool Definition Pattern](./ai-tool-definition-pattern.md) - Creating AI tools with Zod schemas
- [Tool Call Structure](./tool-call-structure.md) - Understanding tool invocations and responses

## Message Handling
- [Message Format Normalization](./message-format-normalization.md) - Converting between message formats

## System Prompts
- [System Prompt Generation](./system-prompt-generation.md) - Core dynamic prompt pattern
- [System Prompt Optimization](./system-prompt-optimization.md) - Templates, caching, and performance

## Observability & Analytics
- [Event Tracking Analytics](./event-tracking-analytics.md) - Dual-destination tracking pattern
- [Event Tracking Chat Events](./event-tracking-chat-events.md) - Chat-specific event tracking

## Coming Soon
- Admin Tool Testing Interface
- Multiple Chat Routes Pattern
- Tool Generator Functions Deep Dive
