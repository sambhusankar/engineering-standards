# AI Tool Input Parameter Pattern

Accept `input` as execute parameter, destructure inside. Preserves full input for error logging.

## Pattern

```javascript
execute: async (input) => {  // ✅ Accept input parameter
  try {
    const { metrics, device_ids, start, end } = input;  // Destructure inside

    const data = await vimanaSVC.getMetrics({ ... });
    return { result: data };
  } catch (error) {
    handleToolError(error, {
      tool_name: "getMetrics",
      tool_input: input,  // ✅ Full input available for debugging
      plant_id: p_id,
    });
  }
}
```

## Anti-Pattern

```javascript
// ❌ BAD - Destructures in parameter list
execute: async ({ metrics, device_ids, start, end }) => {
  try {
    // ...
  } catch (error) {
    handleToolError(error, {
      tool_input: ???,  // ❌ No access to full input!
    });
  }
}
```

## Why This Matters

- **Error Context**: Full input captured for Sentry logging
- **Debugging**: See exact parameters that caused errors
- **Future-Proof**: Adding parameters doesn't break error logging

## Related Notes
- [AI Tool Error Handling](/architecture/ai/ai-tool-error-handling.md)
- [AI Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md)
