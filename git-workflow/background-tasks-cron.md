# Background Tasks: Cron Jobs

Implement scheduled recurring tasks using cron job pattern.

## Pattern (Node.js)

```javascript
// jobs/dailyReport.js
import cron from 'node-cron';
import { generateDailyReport } from '../services/reportService';

// Run every day at 6 AM
cron.schedule('0 6 * * *', async () => {
  console.log('Running daily report generation...');
  try {
    await generateDailyReport();
    console.log('Daily report generated successfully');
  } catch (error) {
    console.error('Failed to generate daily report:', error);
    // Alert monitoring system
    await alertOncall(error);
  }
});
```

## Cron Schedule Format

```
* * * * *
│ │ │ │ │
│ │ │ │ └─── Day of week (0-7, 0 and 7 are Sunday)
│ │ │ └───── Month (1-12)
│ │ └─────── Day of month (1-31)
│ └───────── Hour (0-23)
└─────────── Minute (0-59)
```

## Common Schedules

```javascript
// Every minute
cron.schedule('* * * * *', handler);

// Every hour
cron.schedule('0 * * * *', handler);

// Every day at midnight
cron.schedule('0 0 * * *', handler);

// Every Monday at 9 AM
cron.schedule('0 9 * * 1', handler);

// Every 15 minutes
cron.schedule('*/15 * * * *', handler);
```

## Best Practices

```javascript
async function runScheduledTask() {
  const taskId = `task-${Date.now()}`;

  try {
    // Check idempotency
    const existing = await cache.get(taskId);
    if (existing) {
      console.log('Task already processed');
      return;
    }

    // Set timeout
    const result = await Promise.race([
      performTask(),
      timeout(30000) // 30 second timeout
    ]);

    // Cache result
    await cache.set(taskId, result, 3600);

    console.log('Task completed:', taskId);
  } catch (error) {
    console.error('Task failed:', error);
    await alertOncall(error);
  }
}

cron.schedule('0 * * * *', runScheduledTask);
```

## Related Notes
- [Background Tasks: Job Queues](/git-workflow/background-tasks-queues.md)
- [Environment Variables](/git-workflow/environment-variables.md)
