---
title: 'Authentication'
description: 'Authenticate with the Vibing AI API'
---

All API requests to Vibing AI require authentication. This guide explains the authentication methods available and how to implement them in your applications.

## Authentication Methods

Vibing AI supports the following authentication methods:

1. **OAuth 2.1** - For user-authenticated requests
2. **API Keys** - For service-to-service requests
3. **Session Tokens** - For web/mobile client requests

## OAuth 2.1

OAuth 2.1 is the recommended authentication method for applications that need to access user data or perform actions on behalf of users.

### OAuth Flow

1. **Authorization Request**

   Redirect the user to the Vibing AI authorization URL:

   ```
   https://auth.vibing.im/authorize?
     response_type=code&
     client_id=YOUR_CLIENT_ID&
     redirect_uri=YOUR_REDIRECT_URI&
     scope=read:memory+write:memory&
     state=SECURE_RANDOM_STATE
   ```

   Parameters:
   - `response_type`: Always set to `code`
   - `client_id`: Your application's client ID
   - `redirect_uri`: URL to redirect after authorization
   - `scope`: Space or plus-separated list of requested scopes
   - `state`: Random string to prevent CSRF attacks

2. **Authorization Response**

   After user approval, they will be redirected to your `redirect_uri` with a code:

   ```
   https://your-app.com/callback?code=AUTHORIZATION_CODE&state=SECURE_RANDOM_STATE
   ```

3. **Token Exchange**

   Exchange the authorization code for access and refresh tokens:

   ```bash
   curl -X POST https://auth.vibing.im/token \
     -d client_id=YOUR_CLIENT_ID \
     -d client_secret=YOUR_CLIENT_SECRET \
     -d grant_type=authorization_code \
     -d code=AUTHORIZATION_CODE \
     -d redirect_uri=YOUR_REDIRECT_URI
   ```

   Response:

   ```json
   {
     "access_token": "ACCESS_TOKEN",
     "token_type": "bearer",
     "expires_in": 3600,
     "refresh_token": "REFRESH_TOKEN",
     "scope": "read:memory write:memory"
   }
   ```

4. **Token Refresh**

   When the access token expires, use the refresh token to get a new one:

   ```bash
   curl -X POST https://auth.vibing.im/token \
     -d client_id=YOUR_CLIENT_ID \
     -d client_secret=YOUR_CLIENT_SECRET \
     -d grant_type=refresh_token \
     -d refresh_token=REFRESH_TOKEN
   ```

### Using OAuth Tokens

Include the access token in the `Authorization` header:

```bash
curl -X GET https://api.vibing.im/v1/memory/items \
  -H "Authorization: Bearer ACCESS_TOKEN"
```

## API Keys

API keys are used for service-to-service authentication where user context is not required.

### Generating API Keys

API keys can be generated in the Vibing AI Developer Portal:

1. Go to [https://developers.vibing.im](https://developers.vibing.im)
2. Navigate to API Keys section
3. Click "Generate New Key"
4. Select scopes and permissions
5. Copy and securely store the key

### Using API Keys

Include the API key in the `X-API-Key` header:

```bash
curl -X GET https://api.vibing.im/v1/memory/items \
  -H "X-API-Key: YOUR_API_KEY"
```

## Session Tokens

Session tokens are used for web and mobile clients after user login.

### Obtaining Session Tokens

Session tokens are obtained by authenticating with username and password:

```bash
curl -X POST https://auth.vibing.im/login \
  -d username=user@example.com \
  -d password=secure_password
```

Response:

```json
{
  "session_token": "SESSION_TOKEN",
  "expires_in": 86400
}
```

### Using Session Tokens

Include the session token in the `X-Session-Token` header:

```bash
curl -X GET https://api.vibing.im/v1/memory/items \
  -H "X-Session-Token: SESSION_TOKEN"
```

## SDK Authentication

The Vibing AI SDK provides simplified authentication methods:

```javascript
import { auth } from '@vibing-ai/sdk/auth';

// OAuth authentication
const authResponse = await auth.authenticate({
  method: 'oauth',
  clientId: 'your-client-id',
  scopes: ['read:memory', 'write:memory']
});

// API key authentication
auth.setApiKey('your-api-key');

// Session token authentication
const sessionResponse = await auth.login({
  username: 'user@example.com',
  password: 'secure_password'
});
```

## Security Best Practices

1. **Store tokens securely**
   - Never expose client secrets in client-side code
   - Use secure storage for tokens
   - Clear tokens when no longer needed

2. **Implement proper scopes**
   - Request only the permissions you need
   - Explain to users why each permission is needed

3. **Handle token expiration**
   - Implement token refresh logic
   - Gracefully handle authentication failures

4. **Secure your redirect URI**
   - Use HTTPS for all redirect URIs
   - Validate state parameter to prevent CSRF

5. **Protect API keys**
   - Do not include API keys in client-side code
   - Rotate keys periodically
   - Use different keys for development and production

## Rate Limiting

Authentication requests are subject to rate limiting:

- OAuth token requests: 10 requests per minute per client
- API key requests: 5 requests per second per key
- Login attempts: 5 attempts per minute per IP address

## Next Steps

- [Permissions API](/api-reference/permissions-api) - Learn how to manage permissions
- [Memory API](/api-reference/memory-api) - Access the Unified Memory System
- [Error Handling](/api-reference/error-handling) - Handle authentication errors 