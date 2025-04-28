---
title: 'API Integration Guide'
description: 'Learn how to integrate with the Vibing AI API'
---

# API Integration Guide

This guide provides developers with practical instructions and best practices for integrating with the Vibing AI API. Whether you're building an app, plugin, agent, or external integration, this guide will help you effectively use the platform's capabilities.

## Getting Started

### Prerequisites

Before integrating with the Vibing AI API, ensure you have:

1. A Vibing AI developer account
2. API credentials (client ID and secret)
3. Basic understanding of RESTful APIs and OAuth 2.1
4. Node.js ≥ 20.6.0 LTS (recommended for SDK usage)

### Integration Options

There are several ways to integrate with the Vibing AI platform:

1. **SDK Integration (Recommended)**: Use our official JavaScript/TypeScript SDK
2. **Direct API Calls**: Make HTTP requests directly to our API endpoints
3. **Webhooks**: Receive push notifications for platform events
4. **WebSocket Events**: Subscribe to real-time events

## SDK Integration

The Vibing AI SDK provides the simplest way to integrate with our platform:

```bash
# Install the SDK
npm install @vibing-ai/sdk
```

### Quick Start Example

```typescript
import { createApiClient } from '@vibing-ai/sdk/api';

// Create API client
const api = createApiClient({
  baseUrl: 'https://api.vibing-ai.com/v1',
  authToken: 'USER_AUTH_TOKEN', // Optional, can be provided later
  onError: (error) => {
    console.error('API Error:', error);
  }
});

// Authentication
const loginResponse = await api.auth.login({
  email: 'user@example.com',
  password: 'user-password'
});

// Set token after authentication
api.setAuthToken(loginResponse.session.access_token);

// Memory operations
const memories = await api.memory.search({
  query: 'project meeting notes',
  limit: 5
});

// Memory storage
await api.memory.store({
  content: {
    text: 'Important project deadline: June 15'
  },
  context: {
    project_id: 'project-123',
    scope: 'project'
  },
  tags: ['deadline', 'project']
});
```

## Authentication Flow

### OAuth 2.1 Implementation

For web applications, implement the OAuth 2.1 flow with PKCE:

1. **Authorization Request**

   ```javascript
   // Generate code verifier and challenge
   const codeVerifier = generateRandomString(64);
   const codeChallenge = await generateCodeChallenge(codeVerifier);
   
   // Store code verifier securely (e.g., in session storage)
   sessionStorage.setItem('code_verifier', codeVerifier);
   
   // Redirect to authorization URL
   const authUrl = new URL('https://auth.vibing-ai.com/authorize');
   authUrl.searchParams.append('client_id', CLIENT_ID);
   authUrl.searchParams.append('redirect_uri', REDIRECT_URI);
   authUrl.searchParams.append('response_type', 'code');
   authUrl.searchParams.append('scope', 'profile memory:read');
   authUrl.searchParams.append('state', generateState());
   authUrl.searchParams.append('code_challenge', codeChallenge);
   authUrl.searchParams.append('code_challenge_method', 'S256');
   
   window.location.href = authUrl.toString();
   ```

2. **Handle Redirect and Token Exchange**

   ```javascript
   // In the redirect handler
   const urlParams = new URLSearchParams(window.location.search);
   const code = urlParams.get('code');
   const state = urlParams.get('state');
   
   // Verify state to prevent CSRF
   if (state !== sessionStorage.getItem('state')) {
     throw new Error('Invalid state');
   }
   
   // Exchange code for tokens
   const tokenResponse = await fetch('https://auth.vibing-ai.com/token', {
     method: 'POST',
     headers: {
       'Content-Type': 'application/x-www-form-urlencoded',
     },
     body: new URLSearchParams({
       grant_type: 'authorization_code',
       client_id: CLIENT_ID,
       redirect_uri: REDIRECT_URI,
       code: code,
       code_verifier: sessionStorage.getItem('code_verifier'),
     }).toString(),
   });
   
   const tokens = await tokenResponse.json();
   
   // Store tokens securely
   securelyStoreTokens(tokens);
   ```

### Handling Token Refresh

Implement token refresh logic to maintain uninterrupted access:

```javascript
async function getValidToken() {
  const tokens = getStoredTokens();
  
  // Check if access token is expired
  if (isTokenExpired(tokens.access_token)) {
    // Refresh the token
    const refreshResponse = await fetch('https://auth.vibing-ai.com/token', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: new URLSearchParams({
        grant_type: 'refresh_token',
        client_id: CLIENT_ID,
        refresh_token: tokens.refresh_token,
      }).toString(),
    });
    
    const newTokens = await refreshResponse.json();
    securelyStoreTokens(newTokens);
    return newTokens.access_token;
  }
  
  return tokens.access_token;
}
```

## Memory System Integration

### Storing Information

Store information in the Unified Memory System:

```javascript
async function storeProjectNote(note, projectId) {
  const token = await getValidToken();
  
  const response = await fetch('https://api.vibing-ai.com/v1/memory/store', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      content: {
        text: note,
        type: 'text/plain',
        metadata: {
          source: 'project-notes',
          importance: 'medium'
        }
      },
      context: {
        project_id: projectId,
        scope: 'project',
        namespace: 'notes'
      },
      tags: ['note', 'project', projectId],
      ttl: 30 * 24 * 60 * 60 // 30 days in seconds
    })
  });
  
  return await response.json();
}
```

### Vector Search

Perform semantic search on memory items:

```javascript
async function searchProjectMemory(query, projectId) {
  const token = await getValidToken();
  
  const response = await fetch('https://api.vibing-ai.com/v1/memory/search', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      query: query,
      context: {
        project_id: projectId,
        scope: 'project'
      },
      filters: {
        tags: ['note', 'project']
      },
      limit: 10,
      similarity_threshold: 0.7
    })
  });
  
  return await response.json();
}
```

## Real-time Events Integration

### WebSocket Connection

Connect to the real-time event system:

```javascript
function connectToEventSystem() {
  const token = getValidToken();
  const clientId = generateUniqueId();
  
  const ws = new WebSocket(`wss://api.vibing-ai.com/realtime?token=${token}&channels=conversation,memory&client_id=${clientId}`);
  
  ws.onopen = () => {
    console.log('Connected to event system');
  };
  
  ws.onmessage = (event) => {
    const eventData = JSON.parse(event.data);
    handleEvent(eventData);
  };
  
  ws.onclose = (event) => {
    console.log('Disconnected from event system:', event.code, event.reason);
    // Implement reconnection logic
  };
  
  ws.onerror = (error) => {
    console.error('WebSocket error:', error);
  };
  
  return ws;
}

function handleEvent(event) {
  switch (event.type) {
    case 'message.new':
      handleNewMessage(event.data);
      break;
    case 'memory.updated':
      handleMemoryUpdate(event.data);
      break;
    // Handle other event types
  }
}
```

### Event Subscription Management

Manage event subscriptions for webhooks:

```javascript
async function createEventSubscription(webhookUrl) {
  const token = await getValidToken();
  
  const response = await fetch('https://api.vibing-ai.com/v1/events/subscriptions', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      channels: ['conversation', 'memory'],
      events: ['message.new', 'memory.updated'],
      filters: {
        project_id: 'your-project-id'
      },
      delivery: {
        method: 'webhook',
        url: webhookUrl,
        headers: {
          'X-Custom-Header': 'custom-value'
        }
      }
    })
  });
  
  return await response.json();
}
```

## Vibing Super Agent Integration

### Sending Messages

Send messages to the Vibing Super Agent:

```javascript
async function sendToSuperAgent(message, conversationId) {
  const token = await getValidToken();
  
  const response = await fetch('https://api.vibing-ai.com/v1/agent/message', {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${token}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      conversation_id: conversationId,
      content: message,
      context: {
        project_id: 'your-project-id',
        metadata: {
          source: 'your-app'
        }
      }
    })
  });
  
  return await response.json();
}
```

## Error Handling

Implement proper error handling for API requests:

```javascript
async function apiRequest(url, options) {
  try {
    const response = await fetch(url, options);
    
    if (!response.ok) {
      // Handle HTTP errors
      const errorData = await response.json();
      
      if (response.status === 401) {
        // Handle authentication errors
        await refreshTokenAndRetry();
      } else if (response.status === 429) {
        // Handle rate limiting
        const retryAfter = response.headers.get('Retry-After') || 30;
        await delay(retryAfter * 1000);
        return apiRequest(url, options);
      } else {
        throw new Error(`API Error: ${errorData.detail}`);
      }
    }
    
    return await response.json();
  } catch (error) {
    // Handle network errors
    console.error('API Request Failed:', error);
    throw error;
  }
}
```

## Rate Limiting Considerations

Implement proper handling for rate limits:

```javascript
// Exponential backoff with jitter for retries
function calculateBackoff(retry, initialBackoff = 1000, maxBackoff = 60000) {
  const backoff = Math.min(initialBackoff * Math.pow(2, retry), maxBackoff);
  const jitter = backoff * 0.2 * Math.random();
  return backoff + jitter;
}

async function retryWithBackoff(fn, maxRetries = 5) {
  let retries = 0;
  
  while (true) {
    try {
      return await fn();
    } catch (error) {
      if (error.status !== 429 || retries >= maxRetries) {
        throw error;
      }
      
      const retryAfter = error.headers?.['retry-after'] 
        ? parseInt(error.headers['retry-after']) * 1000 
        : calculateBackoff(retries);
      
      console.log(`Rate limited. Retrying after ${retryAfter}ms`);
      await new Promise(resolve => setTimeout(resolve, retryAfter));
      retries++;
    }
  }
}
```

## Best Practices

1. **Secure Token Storage**
   - Store tokens securely, never in localStorage
   - Use HttpOnly cookies or secure browser storage mechanisms
   - Clear tokens when no longer needed

2. **Efficient API Usage**
   - Batch operations when possible
   - Implement caching for frequently accessed data
   - Use pagination for large data sets

3. **Resource Cleanup**
   - Close WebSocket connections when not in use
   - Unsubscribe from events when components are destroyed
   - Release resources in error scenarios

4. **Error Handling**
   - Implement comprehensive error handling
   - Add retry logic with exponential backoff
   - Provide user-friendly error messages

5. **Performance Optimization**
   - Minimize redundant API calls
   - Optimize payload sizes
   - Use compression for large payloads

6. **Security Considerations**
   - Always use HTTPS
   - Implement proper CSRF protection
   - Request only necessary permissions

## SDK Reference

For detailed SDK documentation, refer to:
- [SDK Overview](/developer-guides/sdk-overview)
- [API Reference](/api-reference/introduction)
- [Authentication](/api-reference/authentication)
- [Memory API](/api-reference/memory-api)
- [Event System](/api-reference/event-system)

## Troubleshooting

### Common Issues and Solutions

- **401 Unauthorized**: Ensure access token is valid and not expired
- **403 Forbidden**: Check if you have the required permissions
- **429 Too Many Requests**: Implement backoff and retry mechanisms
- **Network Errors**: Check connectivity and API status
- **WebSocket Disconnections**: Implement reconnection with backoff

## Support Resources

If you encounter issues with API integration, use these resources:
- [Developer Portal](https://developers.vibing.im)
- [API Status Page](https://status.vibing.im)
- [Community Forum](https://community.vibing.im)
- [Support Contact](mailto:api-support@vibing.im) 