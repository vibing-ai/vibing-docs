---
title: 'Unified Memory System'
description: 'How Vibing AI stores, manages, and retrieves contextual information across applications'
---

# Introduction

The **Unified Memory System** is a core component of Vibing AI that provides persistent storage and cross-application context awareness. It enables applications to share information while maintaining strict privacy controls and purpose-bound permissions.

## Overview

The Unified Memory System serves as the central knowledge repository for the Vibing AI platform, enabling:

- Continuous context across multiple conversations and applications
- Semantic search and retrieval based on meaning, not just keywords
- Structured data with schema validation for type safety
- Permission-controlled access with purpose binding and time limits
- Historical versioning for auditing and recovery

## Memory Architecture

The memory system is organized hierarchically with different scopes designed for specific use cases:

### Global Memory

Persistent across all contexts and available platform-wide:

- User profile and preferences
- Long-term knowledge and history
- Cross-project information

**Access Example:** `memory:read:global;purpose=user_preferences;ttl=session`

### Workspace Memory

Shared across a collection of projects within a workspace:

- Workspace configuration
- Team members and access rights
- High-level objectives and resources

**Access Example:** `memory:write:workspace:{id};purpose=team_collaboration;ttl=30d`

### Project Memory

Associated with a specific project context:

- Project-specific content and assets
- Project history and versions
- Specialized knowledge for the project domain

**Access Example:** `memory:read:project:{id};purpose=content_generation;ttl=1h`

### Session Memory

Limited to the current interaction session:

- Short-term conversation context
- Temporary data and calculations
- Ephemeral states

**Access Example:** `memory:read:session;purpose=conversation_context;ttl=30m`

### Private Memory

Isolated to a specific offering with no cross-application sharing:

- Offering-specific data
- User settings for the offering
- Processing history

**Access Example:** `memory:write:private:{offering_id};purpose=feature_personalization;ttl=90d`

## Key Features

### Vectorized Memory

Content is stored with semantic embeddings that enable retrieval based on meaning rather than exact matching:

```javascript
// Store data with semantic indexing
await memory.set('project-notes', noteContent, {
  embedding: true, // Enable semantic indexing
  metadata: {
    topic: 'architecture',
    importance: 'high'
  }
});

// Semantic search
const results = await memory.search('system security considerations', {
  limit: 5,
  threshold: 0.75
});
```

### Structured Records

Typed data storage with schema validation ensures data integrity:

```javascript
// Define schema
const userPreferencesSchema = {
  type: 'object',
  properties: {
    theme: { type: 'string', enum: ['light', 'dark', 'system'] },
    notifications: { type: 'boolean' },
    fontSize: { type: 'number', minimum: 8, maximum: 24 }
  },
  required: ['theme', 'notifications']
};

// Store with schema validation
await memory.set('user-preferences', {
  theme: 'dark',
  notifications: true,
  fontSize: 16
}, { schema: userPreferencesSchema });
```

### Permission System

Granular access controls ensure data is only accessed with appropriate permissions:

```javascript
// Request memory access permission
const granted = await permissions.request(
  'memory:read:project:12345;purpose=data_analysis;ttl=1h'
);

if (granted) {
  // Access granted for the specified purpose and duration
  const projectData = await memory.get('project:12345:data');
}
```

### Temporal Management

Historical versioning and time-based constraints protect data integrity:

```javascript
// Get current version
const currentData = await memory.get('document:draft');

// Get historical version
const previousData = await memory.getVersion('document:draft', {
  version: 'v3',
  // or alternatively by timestamp
  // timestamp: '2023-06-15T12:34:56Z'
});

// View version history
const history = await memory.getHistory('document:draft', {
  limit: 10
});
```

### Notification System

Event-based triggers for memory changes enable reactive behavior:

```javascript
// Subscribe to memory changes
memory.subscribe('user-preferences', (newValue, oldValue) => {
  console.log('Preferences updated:', newValue);
});

// Subscribe with a filter
memory.subscribe('project:*:status', 
  (newValue, oldValue, key) => {
    console.log(`Project status updated: ${key}`, newValue);
  },
  { filter: value => value.status === 'completed' }
);
```

## Using the Unified Memory System

### Basic Operations

```javascript
// Import memory module
import { memory } from '@vibing-ai/sdk/memory';

// Store data
await memory.set('user-notes', 'Meeting notes content here');

// Retrieve data
const notes = await memory.get('user-notes');

// Update data
await memory.update('user-preferences', {
  theme: 'light'
});

// Delete data
await memory.delete('temporary-calculation');

// Check if a key exists
const exists = await memory.exists('project-outline');
```

### Advanced Queries

```javascript
// Query with filtering
const results = await memory.query({
  type: 'message',
  filters: {
    conversation: 'current',
    timeRange: { 
      start: '2023-01-01', 
      end: '2023-01-31' 
    },
    contains: 'project goals'
  },
  sort: { createdAt: 'desc' },
  limit: 20
});
```

### Memory Hooks (React)

```javascript
import { useMemory } from '@vibing-ai/sdk/memory/hooks';

function UserPreferencesComponent() {
  // Basic memory hook
  const [preferences, setPreferences] = useMemory('user-preferences');
  
  // Subscribe to changes with filtering
  const [messages, setMessages] = useMemory('conversation-messages', {
    subscribe: true,
    filters: { 
      conversation: 'current',
      after: new Date('2023-06-01')
    }
  });
  
  // Component implementation
  return (
    <div>
      <h2>User Preferences</h2>
      <button onClick={() => setPreferences({
        ...preferences,
        theme: preferences.theme === 'dark' ? 'light' : 'dark'
      })}>
        Toggle Theme
      </button>
      {/* Component UI */}
    </div>
  );
}
```

## Best Practices

### 1. Use Appropriate Scopes

Choose the most appropriate memory scope for your data. Don't store data in global memory if it only belongs to a specific project or session.

### 2. Clear Purpose Declarations

Always specify a clear, accurate purpose when requesting memory permissions. This helps users understand why data access is needed.

### 3. Minimal TTL Durations

Request the shortest time-to-live (TTL) duration necessary for your use case. This enhances security and privacy.

### 4. Structured Data

Use schemas for structured data to ensure type safety and consistency.

### 5. Memory Cleanup

Clean up temporary data when it's no longer needed to prevent memory bloat.

## Security Considerations

The Unified Memory System implements several security measures:

- **Encryption**: All memory data is encrypted at rest and in transit
- **Permission Enforcement**: Strict permission checking before any memory access
- **Audit Logging**: Comprehensive logs of all memory operations
- **Data Isolation**: Clear boundaries between different memory scopes
- **Automatic Expiration**: Time-limited permissions with automatic revocation

## Next Steps

- Learn about [Permission Model](/key-concepts/permission-model) for securing memory access
- Explore [Vibing Super Agent](/key-concepts/vibing-super-agent) integration with memory
- Understand [Interface Surfaces](/key-concepts/interface-surfaces) for presenting memory data
- Review the [Memory API Reference](/api-reference/memory-api) for detailed method specifications 