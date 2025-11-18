# Service Layer Pattern

Centralize API calls in service modules rather than scattering them throughout components.

## Pattern

Create dedicated service modules for API interactions:

```javascript
// services/userService.js
import { apiClient } from './apiClient';

export const userService = {
  async getUser(userId) {
    const response = await apiClient.get(`/users/${userId}`);
    return response.data;
  },

  async updateUser(userId, updates) {
    const response = await apiClient.put(`/users/${userId}`, updates);
    return response.data;
  },

  async deleteUser(userId) {
    await apiClient.delete(`/users/${userId}`);
  },

  async listUsers(filters = {}) {
    const response = await apiClient.get('/users', { params: filters });
    return response.data;
  },
};
```

## Usage in Components

```javascript
import { userService } from '@/services/userService';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    userService.getUser(userId).then(setUser);
  }, [userId]);

  const handleUpdate = async (updates) => {
    await userService.updateUser(userId, updates);
    setUser(prev => ({ ...prev, ...updates }));
  };

  return <div>{/* render user */}</div>;
}
```

## Benefits

- **Centralization**: All API calls in one place
- **Consistency**: Uniform error handling and data transformation
- **Testability**: Easy to mock service layer
- **Maintainability**: Change API structure in one place

## API Client Configuration

```javascript
// services/apiClient.js
import axios from 'axios';

export const apiClient = axios.create({
  baseURL: process.env.API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Add auth token to requests
apiClient.interceptors.request.use((config) => {
  const token = getAuthToken();
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Handle auth errors
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      redirectToLogin();
    }
    return Promise.reject(error);
  }
);
```

## Related Notes
- [API Error Handling](./error-handling-api.md)
- [Custom Hooks for Data Fetching](./custom-hooks.md)
- [Repository Pattern](./repository-pattern.md)
