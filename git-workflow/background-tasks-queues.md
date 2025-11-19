# Background Tasks: Job Queues

Implement asynchronous task processing using job queue pattern (Bull/BullMQ).

## Pattern

```javascript
// queues/emailQueue.js
import Queue from 'bull';

export const emailQueue = new Queue('email', {
  redis: process.env.REDIS_URL,
});

// Define job processor
emailQueue.process(async (job) => {
  const { to, subject, body } = job.data;
  console.log(`Sending email to ${to}...`);
  await sendEmail(to, subject, body);
  console.log(`Email sent to ${to}`);
});

// Add job to queue
export async function queueEmail(to, subject, body) {
  await emailQueue.add(
    { to, subject, body },
    {
      attempts: 3,
      backoff: {
        type: 'exponential',
        delay: 2000,
      },
    }
  );
}
```

## Usage

```javascript
// In API route or service
import { queueEmail } from './queues/emailQueue';

async function registerUser(userData) {
  const user = await createUser(userData);

  // Queue email asynchronously
  await queueEmail(
    user.email,
    'Welcome!',
    `Hi ${user.name}, welcome to our service!`
  );

  return user;
}
```

## Job Options

```javascript
emailQueue.add(
  { to, subject, body },
  {
    attempts: 3,              // Retry up to 3 times
    backoff: {
      type: 'exponential',    // Exponential backoff
      delay: 2000,            // Start with 2s delay
    },
    delay: 5000,              // Delay job by 5 seconds
    priority: 1,              // Higher priority = processed first
    removeOnComplete: true,   // Auto-remove on completion
  }
);
```

## Error Handling

```javascript
emailQueue.on('failed', (job, err) => {
  console.error(`Job ${job.id} failed:`, err);
  // Alert if critical
  if (job.opts.priority === 1) {
    alertOncall(err);
  }
});

emailQueue.on('completed', (job) => {
  console.log(`Job ${job.id} completed`);
});
```

## When to Use Queues

**Use job queues for:**
- Email sending
- Image processing
- Report generation
- Data imports/exports
- Any long-running tasks

**Benefits:**
- Async processing doesn't block request
- Automatic retries on failure
- Job prioritization
- Distributed processing

## Related Notes
- [Background Tasks: Cron Jobs](/git-workflow/background-tasks-cron.md)
- [Environment Variables](/git-workflow/environment-variables.md)
