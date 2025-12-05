# AI Tool Testing Pattern

Test AI tools with mocked dependencies using Arrange-Act-Assert pattern.

## Test Setup

```javascript
jest.mock("ai", () => ({
  tool: jest.fn((config) => ({
    description: config.description,
    inputSchema: config.inputSchema,
    execute: config.execute,
  })),
}));

jest.mock("@sentry/nextjs", () => ({ captureException: jest.fn() }));
jest.mock("@/utils/vimanaSVC", () => ({ getMetrics: jest.fn() }));

import { generateGetMetrics } from "./getMetrics";
import * as vimanaSVC from "@/utils/vimanaSVC";
import * as Sentry from "@sentry/nextjs";
```

## Basic Test Structure

```javascript
describe("generateGetMetrics", () => {
  const mockCookieStore = { get: jest.fn() };
  const mockPlantId = "plant123";

  beforeEach(() => jest.clearAllMocks());

  it("should return metrics data", async () => {
    // Arrange
    vimanaSVC.getMetrics.mockResolvedValue([{ oee: 85 }]);
    const getMetrics = generateGetMetrics({
      cookieStore: mockCookieStore,
      p_id: mockPlantId
    });

    // Act
    const result = await getMetrics.execute({
      metrics: ["oee"],
      start: "2024-01-01T00:00:00Z",
      end: "2024-01-02T00:00:00Z",
    });

    // Assert
    expect(result.result).toHaveLength(1);
    expect(vimanaSVC.getMetrics).toHaveBeenCalledWith(
      expect.objectContaining({ plant: mockPlantId })
    );
  });
});
```

## Testing Error Handling

```javascript
it("should call handleToolError on API failure", async () => {
  vimanaSVC.getMetrics.mockRejectedValue(new Error("API Error"));

  const getMetrics = generateGetMetrics({
    cookieStore: mockCookieStore,
    p_id: mockPlantId
  });

  await expect(getMetrics.execute({
    metrics: ["oee"],
    start: "2024-01-01T00:00:00Z",
    end: "2024-01-02T00:00:00Z",
  })).rejects.toEqual(expect.objectContaining({ message: "API Error" }));

  expect(Sentry.captureException).toHaveBeenCalledWith(
    expect.objectContaining({ message: "API Error" }),
    expect.objectContaining({
      tags: expect.objectContaining({ tool: "getMetrics" })
    })
  );
});
```

## Coverage Requirements

- ✅ Happy path with valid input
- ✅ Service called with correct parameters
- ✅ Optional parameters (with and without)
- ✅ Error handling and Sentry logging
- ✅ Edge cases (empty results, null values)

## Related Notes
- [AI Tool Definition Pattern](/architecture/ai/ai-tool-definition-pattern.md)
- [AI Tool Error Handling](/architecture/ai/ai-tool-error-handling.md)
- [AI Tool Service Abstraction](/architecture/ai/ai-tool-service-abstraction.md)
