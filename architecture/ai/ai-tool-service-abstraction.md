# AI Tool Service Abstraction Pattern

Call service layer functions (e.g., `vimanaSVC`) instead of direct API calls.

## Pattern

```javascript
import * as vimanaSVC from "@/utils/vimanaSVC";  // ✅ Service abstraction

execute: async (input) => {
  const { metrics, start, end } = input;

  // Service handles URL, headers, parsing, errors
  const data = await vimanaSVC.getMetrics({
    cookieStore,
    plant: p_id,
    start, end,
    metrics,
  });

  return { result: data };
}
```

## Anti-Pattern

```javascript
// ❌ BAD - Direct API construction
execute: async (input) => {
  const url = `/api/v3/metrics?plant=${p_id}&start=${start}`;
  const data = await fetchSVC({ url, method: 'GET', cookieStore });
  // Manual URL, headers, error handling scattered across tools
}
```

## Benefits

- **Single Source of Truth**: API logic centralized
- **Easy Testing**: Mock service instead of fetch
- **Consistent Transformations**: Unit conversions in one place
- **Refactoring**: Change API without touching tools

## Service Naming

Match service functions to tool names:

```javascript
vimanaSVC.getMetrics()      // Tool: generateGetMetrics
vimanaSVC.getDevices()      // Tool: generateGetDevices
vimanaSVC.getAlerts()       // Tool: generateGetAlerts
```

## Related Notes
- [AI Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md)
- [AI Tool Testing](/architecture/ai/ai-tool-testing.md)
