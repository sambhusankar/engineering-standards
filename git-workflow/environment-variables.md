# Environment Variables Management

Manage environment-specific configuration using environment files.

## File Structure

```
.env.local          # Local development (not committed)
.env.development    # Development defaults (committed)
.env.staging        # Staging configuration (committed)
.env.production     # Production configuration (committed, no secrets)
.env.example        # Template (committed)
```

## .env.example Template

```bash
# API Configuration
API_BASE_URL=https://api.example.com
API_TIMEOUT=10000

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/dbname

# Authentication
AUTH_SECRET=generate-random-secret-here
JWT_EXPIRY=7d

# External Services
STRIPE_PUBLIC_KEY=pk_test_xxxxx
STRIPE_SECRET_KEY=sk_test_xxxxx

# Feature Flags
ENABLE_BETA_FEATURES=false
```

## .gitignore

Always ignore local environment files:

```
.env.local
.env*.local
```

## Loading Environment Variables

```javascript
// config/env.js
const required = [
  'DATABASE_URL',
  'API_KEY',
  'AUTH_SECRET',
];

// Validate required variables
for (const key of required) {
  if (!process.env[key]) {
    throw new Error(`Missing required environment variable: ${key}`);
  }
}

export const config = {
  database: {
    url: process.env.DATABASE_URL,
  },
  api: {
    key: process.env.API_KEY,
    timeout: parseInt(process.env.API_TIMEOUT || '10000'),
  },
  auth: {
    secret: process.env.AUTH_SECRET,
    jwtExpiry: process.env.JWT_EXPIRY || '7d',
  },
};
```

## Never Commit Secrets

**Don't commit:**
- API keys
- Database passwords
- Private keys
- OAuth secrets

**Do commit:**
- Public API keys (clearly marked)
- Configuration structure
- Default values
- Feature flags

## Using Secret Managers

For production, use dedicated secret management:

```javascript
// config/secrets.js
import { SecretsManager } from 'aws-sdk';

const client = new SecretsManager({ region: 'us-east-1' });

export async function getSecret(name) {
  const result = await client.getSecretValue({ SecretId: name }).promise();
  return JSON.parse(result.SecretString);
}

// Usage
const dbCreds = await getSecret('production/database');
```

## Related Notes
- [Deployment Process](./deployment-process.md)
- [Configuration Management](./configuration-best-practices.md)
