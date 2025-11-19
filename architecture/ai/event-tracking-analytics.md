# Event Tracking & Analytics Pattern

Track AI chatbot interactions for observability, analytics, and compliance using dual-destination pattern.

## Dual-Destination Tracking Pattern

Track all events to both database and external analytics service:

```javascript
import { init, track, identify, Identify } from '@amplitude/analytics-node';
import { getDB } from '@/database';

// Initialize analytics
init(process.env.AMPLITUDE_API_KEY);

/**
 * Generic event tracking function
 * Logs to both database (reliable) and Amplitude (analytics)
 */
export async function trackEvent(
  eventName,
  userId,
  contextId,
  eventProperties = {},
  userProperties = {}
) {
  try {
    // 1. Database tracking (always reliable)
    try {
      const db = getDB();
      await db.EventLog.create({
        user: userId,
        context: contextId,
        name: eventName,
        details: eventProperties,
        created_at: new Date(),
      });
      console.log(`[Database] Logged ${eventName}`);
    } catch (dbError) {
      console.error('[Database] Error:', dbError);
      // Continue to Amplitude even if DB fails
    }

    // 2. External analytics (optional, best-effort)
    try {
      if (userProperties && Object.keys(userProperties).length > 0) {
        const identifyObj = new Identify();
        Object.keys(userProperties).forEach(key => {
          identifyObj.set(key, userProperties[key]);
        });
        identify(identifyObj, { user_id: userId });
      }

      track(eventName, { ...eventProperties, context_id: contextId }, {
        user_id: userId,
      });
      console.log(`[Amplitude] Tracked ${eventName}`);
    } catch (amplitudeError) {
      console.error('[Amplitude] Error:', amplitudeError);
      // Don't throw - database tracking already succeeded
    }
  } catch (error) {
    console.error('[Analytics] Error:', error);
    // Never throw - don't let analytics break app
  }
}
```

## Related Notes
- [AI SDK Server Integration](/architecture/ai/ai-sdk-server-streaming.md) - Where tracking is integrated
- [Event Tracking Chat Events](/architecture/ai/event-tracking-chat-events.md) - Chat-specific event tracking
- [Tool Call Structure](/architecture/ai/tool-call-structure.md) - Understanding tool calls
