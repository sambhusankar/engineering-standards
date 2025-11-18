# System Prompt Optimization

Optimize system prompts for performance, reusability, and cost efficiency.

## Template-Based Prompts

Use templates with placeholders for consistent, reusable prompts:

```javascript
const ASSISTANT_TEMPLATE = `You are a helpful assistant.

## Context Information
- Name: {CONTEXT_NAME}
- Timezone: {TIMEZONE}
- Current Time: {CURRENT_TIME}

## Available Resources ({RESOURCE_COUNT} total)
{RESOURCE_LIST}

## Available Capabilities
{CAPABILITY_LIST}

{CUSTOM_INSTRUCTIONS}

## Communication Style
- Data-driven and specific
- Professional terminology
- Concise but complete responses`;

function generatePromptFromTemplate(template, values) {
  let prompt = template;
  Object.entries(values).forEach(([key, value]) => {
    prompt = prompt.replace(`{${key}}`, value);
  });
  return prompt;
}

// Usage
const prompt = generatePromptFromTemplate(ASSISTANT_TEMPLATE, {
  CONTEXT_NAME: context.name,
  TIMEZONE: context.timezone,
  CURRENT_TIME: currentTime,
  RESOURCE_COUNT: resources.length,
  RESOURCE_LIST: resourceList,
  CAPABILITY_LIST: capabilityList,
  CUSTOM_INSTRUCTIONS: customInstructions
});
```

## Conditional Prompt Sections

Build prompts dynamically based on context:

```javascript
/**
 * Build prompt with conditional sections based on context
 */
function buildDynamicPrompt(context) {
  const sections = [
    'You are a helpful assistant.',
    `Working with context: ${context.name}`,
  ];

  // Add time information if available
  if (context.current_period) {
    sections.push(`Current period: ${context.current_period}`);
  }

  // Add mode notice
  if (context.in_special_mode) {
    sections.push('⚠️ Context is currently in special mode');
  }

  // Add resource-specific info if provided
  if (context.resource) {
    sections.push(`\n## Resource Context`);
    sections.push(`Resource: ${context.resource.name}`);
    if (context.resource.status === 'unavailable') {
      sections.push(`Status: UNAVAILABLE - last available ${context.resource.unavailable_minutes} minutes ago`);
    }
  }

  // Add available tools/queries
  if (context.availableTools?.length) {
    sections.push('\n## Available Queries');
    context.availableTools.forEach(tool => {
      sections.push(`- ${tool.name}: ${tool.description}`);
    });
  }

  return sections.join('\n');
}
```

## Prompt Versioning for A/B Testing

```javascript
// Store prompt versions for testing and comparison
const PROMPTS = {
  v1: {
    name: 'Original',
    template: `You are a helpful assistant...`,
    created_at: '2024-01-01',
    performance_score: null,
    user_satisfaction: null
  },
  v2: {
    name: 'With Guidance',
    template: `You are a helpful assistant with detailed guidance...`,
    created_at: '2024-02-01',
    performance_score: null,
    user_satisfaction: null
  },
  v3: {
    name: 'Concise',
    template: `You are a concise assistant...`,
    created_at: '2024-03-01',
    performance_score: null,
    user_satisfaction: null
  }
};

// Track which version is used
function selectPromptVersion(context) {
  // A/B test: 50% v1, 50% v2
  const useV2 = Math.random() > 0.5;
  return useV2 ? 'v2' : 'v1';
}

// Log version in analytics
await trackEvent('chat_started', {
  user_id,
  prompt_version: selectedVersion,
  // ... other metrics
});
```

## Performance Optimization

### Token Usage Minimization

```javascript
// ❌ Bad - includes all resources (expensive)
const allResourcesText = resources.map(r => `- ${r.name} (${r.uuid})`).join('\n');

// ✅ Good - limit to top resources + summary
function summarizeResources(resources, maxCount = 10) {
  const topResources = resources.slice(0, maxCount);
  const otherCount = resources.length - maxCount;

  let text = topResources.map(r => `- ${r.name}`).join('\n');

  if (otherCount > 0) {
    text += `\n... and ${otherCount} more resources`;
  }

  return text;
}
```

### Caching Strategy

```javascript
// Cache prompts that don't change frequently
const promptCache = new Map();

function getCachedPrompt(key, generator) {
  if (promptCache.has(key)) {
    return promptCache.get(key);
  }

  const prompt = generator();
  promptCache.set(key, prompt);

  // Clear cache after 1 hour
  setTimeout(() => promptCache.delete(key), 60 * 60 * 1000);

  return prompt;
}

// Usage
const prompt = getCachedPrompt(`context-${context_id}`, () => {
  return generateSystemPrompt(context_id, cookieStore);
});
```

## Token Usage Awareness

System prompt length directly impacts API costs and latency:

```javascript
// Estimate tokens in prompt (rough: 1 token ≈ 4 characters)
function estimateTokens(prompt) {
  return Math.ceil(prompt.length / 4);
}

// Longer prompts = higher cost and latency
// Balance context completeness with efficiency
```

## Security Best Practices

Never include sensitive information in system prompts:

```javascript
// ❌ NEVER include
// - Authentication credentials (API keys, tokens)
// - Internal infrastructure details (endpoints, database URLs)
// - User PII or private information
// - Security-sensitive operational details

// ✅ OK to include
// - Public context IDs
// - Timezone/location info
// - General resource names
// - Public capability descriptions
```

## Related Notes
- [System Prompt Generation](./system-prompt-generation.md) - Core prompt generation
