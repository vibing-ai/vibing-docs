---
title: 'Event System API'
description: 'Real-time event system for the Vibing AI platform'
---

# Event System API

The Event System enables real-time communication between platform components and with client applications, providing a consistent way to receive updates and trigger actions across the platform.

## Overview

The Vibing AI Event System uses WebSockets for real-time communication, with HTTP-based subscription management and event publication. It supports various event types across different channels for a comprehensive eventing solution.

## WebSocket Connection

### Connection Endpoint

```
wss://api.vibing-ai.com/realtime
```

### Connection Parameters

```
?token=ACCESS_TOKEN
&channels=conversation,memory,offering
&client_id=CLIENT_ID
```

Required parameters:
- `token`: A valid access token with appropriate permissions
- `channels`: Comma-separated list of channels to subscribe to
- `client_id`: Unique identifier for the client connection

## Event Types

The event system supports a diverse set of event types across different channels:

### Conversation Events

- `message.new` - New message in conversation
- `message.updated` - Message content updated
- `conversation.created` - New conversation created
- `conversation.updated` - Conversation metadata changed
- `typing.start` - User/agent started typing
- `typing.stop` - User/agent stopped typing

### Memory Events

- `memory.created` - New memory item created
- `memory.updated` - Memory item updated
- `memory.deleted` - Memory item deleted
- `memory.embedding_complete` - Embedding generation completed

### Offering Events

- `offering.installed` - New offering installed
- `offering.uninstalled` - Offering uninstalled
- `offering.updated` - Offering updated
- `offering.status_change` - Offering status changed

### System Events

- `system.notification` - System notification
- `system.status` - System status update
- `system.maintenance` - Maintenance notification

## Event Message Format

All events follow a standardized format:

```json
{
  "id": "evt_12345",
  "type": "message.new",
  "channel": "conversation",
  "timestamp": "2023-04-10T16:30:00Z",
  "data": {
    "conversation_id": "conv_6789",
    "message": {
      "id": "msg_12345",
      "content": "Hello world",
      "sender": {
        "type": "user",
        "id": "user_1234"
      },
      "created_at": "2023-04-10T16:30:00Z"
    }
  }
}
```

Event properties:
- `id`: Unique event identifier
- `type`: Event type (e.g., `message.new`)
- `channel`: Event channel (e.g., `conversation`)
- `timestamp`: Event creation time in ISO 8601 format
- `data`: Event-specific payload (varies by event type)

## REST API Endpoints

Besides WebSocket connections, the event system provides REST API endpoints for various operations.

### Publishing Events

**Endpoint:** `POST /api/events/publish`

**Authentication Required:** Yes

**Request:**

```json
{
  "type": "custom.event",
  "channel": "app-channel",
  "data": {
    "customField": "value",
    "timestamp": "2023-04-10T16:35:00Z"
  },
  "targets": ["user-id-1", "user-id-2"]
}
```

**Response:**

```json
{
  "event_id": "evt_12345",
  "published": true,
  "timestamp": "2023-04-10T16:35:05Z",
  "recipients": 2
}
```

### Managing Subscriptions

**Endpoint:** `POST /api/events/subscriptions`

**Authentication Required:** Yes

**Request:**

```json
{
  "channels": ["conversation", "memory"],
  "events": ["message.new", "memory.updated"],
  "filters": {
    "conversation_id": "conv_6789"
  },
  "delivery": {
    "method": "webhook",
    "url": "https://example.com/webhook",
    "headers": {
      "X-Custom-Header": "value"
    }
  }
}
```

**Response:**

```json
{
  "subscription_id": "sub_12345",
  "channels": ["conversation", "memory"],
  "events": ["message.new", "memory.updated"],
  "filters": {
    "conversation_id": "conv_6789"
  },
  "delivery": {
    "method": "webhook",
    "url": "https://example.com/webhook"
  },
  "created_at": "2023-04-10T16:40:00Z",
  "status": "active"
}
```

### Listing Subscriptions

**Endpoint:** `GET /api/events/subscriptions`

**Authentication Required:** Yes

**Response:**

```json
{
  "subscriptions": [
    {
      "subscription_id": "sub_12345",
      "channels": ["conversation", "memory"],
      "events": ["message.new", "memory.updated"],
      "filters": {
        "conversation_id": "conv_6789"
      },
      "delivery": {
        "method": "webhook",
        "url": "https://example.com/webhook"
      },
      "created_at": "2023-04-10T16:40:00Z",
      "status": "active"
    }
  ],
  "total": 1
}
```

### Deleting a Subscription

**Endpoint:** `DELETE /api/events/subscriptions/:subscription_id`

**Authentication Required:** Yes

**Response:**

```json
{
  "subscription_id": "sub_12345",
  "deleted": true
}
```

## SDK Integration

The Vibing AI SDK provides simple methods for working with the Event System:

```typescript
import { createEventClient } from '@vibing-ai/sdk/events';

// Create WebSocket event client
const events = createEventClient({
  url: 'wss://api.vibing-ai.com/realtime',
  authToken: 'USER_AUTH_TOKEN',
  channels: ['conversation', 'memory']
});

// Subscribe to events
events.subscribe('message.new', (message) => {
  console.log('New message:', message);
});

events.subscribe('memory.updated', (memory) => {
  console.log('Memory updated:', memory);
});

// Connection management
events.on('connect', () => {
  console.log('Connected to event system');
});

events.on('disconnect', (reason) => {
  console.log('Disconnected:', reason);
});

// Cleanup
function cleanup() {
  events.disconnect();
}
```

## React Hook Integration

For React applications, the SDK provides a convenient hook:

```typescript
import { useEvents } from '@vibing-ai/sdk/events';

function ConversationComponent() {
  const { subscribe, publish, connected } = useEvents({
    channels: ['conversation']
  });
  
  useEffect(() => {
    const unsubscribe = subscribe('message.new', (event) => {
      // Handle new message
    });
    
    return unsubscribe;
  }, [subscribe]);
  
  const sendCustomEvent = () => {
    publish({
      type: 'custom.event',
      channel: 'conversation',
      data: { /* event data */ }
    });
  };
  
  return (
    <div>
      {connected ? 'Connected' : 'Connecting...'}
      <button onClick={sendCustomEvent}>Send Event</button>
    </div>
  );
}
```

## Webhook Integration

When using webhooks for event delivery, your server should handle POST requests with the following format:

```json
{
  "subscription_id": "sub_12345",
  "events": [
    {
      "id": "evt_12345",
      "type": "message.new",
      "channel": "conversation",
      "timestamp": "2023-04-10T16:30:00Z",
      "data": {
        "conversation_id": "conv_6789",
        "message": {
          "id": "msg_12345",
          "content": "Hello world",
          "sender": {
            "type": "user",
            "id": "user_1234"
          },
          "created_at": "2023-04-10T16:30:00Z"
        }
      }
    }
  ],
  "timestamp": "2023-04-10T16:30:01Z"
}
```

Your server should respond with a 200 OK status code to acknowledge receipt. If the webhook delivery fails, the system will retry with exponential backoff.

## Best Practices

1. **Efficient Event Handling**: Process events asynchronously and quickly to avoid blocking the event loop
2. **Reconnection Logic**: Implement proper reconnection with backoff for WebSocket connections
3. **Targeted Subscriptions**: Subscribe only to events you need to reduce network traffic
4. **Filter Usage**: Use filters to narrow down events based on context
5. **Error Handling**: Implement robust error handling for network issues and malformed events
6. **Connection Cleanup**: Always disconnect WebSocket connections when no longer needed
7. **Idempotent Processing**: Process events idempotently to handle potential duplicates

## Rate Limits

- WebSocket connections: 10 concurrent connections per user
- Event publication: 20 events per minute per user
- Subscription creation: 10 per hour per user
- Event delivery: 100 events per minute per subscription

## Security Considerations

1. **Token Security**: Protect access tokens used for WebSocket connections
2. **Webhook Authentication**: Verify webhook requests using signature verification
3. **Event Validation**: Validate event data before processing
4. **Connection Monitoring**: Monitor for unusual connection patterns
5. **Secure URLs**: Use HTTPS for webhook URLs
6. **Data Minimization**: Only send necessary data in events

## AsyncAPI Documentation

The complete Event System is documented using AsyncAPI 2.6, which provides detailed schema information for all events. The AsyncAPI specification is available at `/api-reference/asyncapi.json`.

## Next Steps

- [Memory API](/api-reference/memory-api) - Learn how to use memory events
- [Authentication](/api-reference/authentication) - Understand event authentication
- [SDK Overview](/developer-guides/sdk-overview) - Explore the SDK for Events integration 