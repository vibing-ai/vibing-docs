---
title: 'Unified Memory System API'
description: 'API for accessing the Vibing AI Unified Memory System'
---

The Unified Memory System API provides interfaces for storing, retrieving, and searching context-aware memories, which is a cornerstone of the Vibing AI platform.

## Core Concepts

1. **Memory Items**: Discrete pieces of information stored in the memory system
2. **Projects**: Namespaces for organizing memory items
3. **Contexts**: Scopes for memory items (global, project, conversation, document, session)
4. **Embeddings**: Vector representations of memory items for semantic search
5. **Permissions**: Access controls for memory items

## Memory Access Patterns

The Unified Memory System supports multiple access patterns:

### Direct API Client (Recommended)

```typescript
import { createApiClient } from '@vibing-ai/sdk/api';

const api = createApiClient({ /* config */ });

// Store memory
await api.memory.store({
  content: { text: "Important information" },
  context: { project_id: "project-id", scope: "project" }
});

// Retrieve memory
const memories = await api.memory.search({ query: "important" });
```

### React Hook Pattern

```typescript
import { useMemory } from '@vibing-ai/sdk/common/memory';

function MyComponent() {
  const { store, retrieve, search } = useMemory({
    subscribe: true
  });
  
  const handleSave = async () => {
    await store({
      content: { text: "New information" },
      context: { scope: "conversation" }
    });
  };
  
  return <button onClick={handleSave}>Save</button>;
}
```

### Legacy Window Pattern (Deprecated)

```typescript
// This pattern is deprecated and should not be used in new code
window.memory.set("key", "value");
const value = window.memory.get("key");
```

Always prefer the direct API client pattern for new development, as it provides type safety, proper error handling, and follows modern JavaScript practices.

## Endpoints

### Store Memory Item

Store a new memory item in the system.

**Endpoint:** `POST /api/memory/store`

**Authentication Required:** Yes

**Request:**

```json
{
  "content": {
    "text": "Meeting notes for Project Alpha",
    "type": "text/plain",
    "metadata": {
      "source": "conversation",
      "importance": "high"
    }
  },
  "context": {
    "project_id": "project-id",
    "scope": "project",
    "namespace": "notes"
  },
  "tags": ["meeting", "project-alpha", "notes"],
  "ttl": 2592000  // 30 days in seconds
}
```

**Response:**

```json
{
  "id": "memory-id",
  "content": {
    "text": "Meeting notes for Project Alpha",
    "type": "text/plain",
    "metadata": {
      "source": "conversation",
      "importance": "high"
    }
  },
  "context": {
    "project_id": "project-id",
    "scope": "project",
    "namespace": "notes"
  },
  "tags": ["meeting", "project-alpha", "notes"],
  "embedding_status": "completed",
  "embedding_model": "text-embedding-3-small",
  "created_at": "2023-04-10T14:00:00Z",
  "expires_at": "2023-05-10T14:00:00Z",
  "created_by": "user-id",
  "updated_at": "2023-04-10T14:05:00Z"
}
```

### Retrieve Memory Item

Retrieve a specific memory item by ID.

**Endpoint:** `GET /api/memory/retrieve?id=memory-id`

**Authentication Required:** Yes

**Response:**

```json
{
  "id": "memory-id",
  "content": {
    "text": "Meeting notes for Project Alpha",
    "type": "text/plain",
    "metadata": {
      "source": "conversation",
      "importance": "high"
    }
  },
  "context": {
    "project_id": "project-id",
    "scope": "project",
    "namespace": "notes"
  },
  "tags": ["meeting", "project-alpha", "notes"],
  "embedding_status": "completed",
  "embedding_model": "text-embedding-3-small",
  "created_at": "2023-04-10T14:00:00Z",
  "expires_at": "2023-05-10T14:00:00Z",
  "created_by": "user-id",
  "updated_at": "2023-04-10T14:05:00Z"
}
```

### Vector Search

Search for memory items using semantic vector search.

**Endpoint:** `POST /api/memory/search`

**Authentication Required:** Yes

**Request:**

```json
{
  "query": "Project Alpha meeting discussion",
  "context": {
    "project_id": "project-id",
    "scope": "project"
  },
  "filters": {
    "tags": ["meeting", "notes"],
    "created_at": {
      ">=": "2023-01-01T00:00:00Z"
    }
  },
  "limit": 5,
  "similarity_threshold": 0.8
}
```

**Response:**

```json
{
  "results": [
    {
      "id": "memory-id-1",
      "content": {
        "text": "Meeting notes for Project Alpha",
        "type": "text/plain",
        "metadata": {
          "source": "conversation",
          "importance": "high"
        }
      },
      "similarity": 0.92,
      "context": {
        "project_id": "project-id",
        "scope": "project",
        "namespace": "notes"
      },
      "created_at": "2023-04-10T14:00:00Z"
    },
    {
      "id": "memory-id-2",
      "content": {
        "text": "Project Alpha discussion about timeline",
        "type": "text/plain",
        "metadata": {
          "source": "conversation",
          "importance": "medium"
        }
      },
      "similarity": 0.85,
      "context": {
        "project_id": "project-id",
        "scope": "project",
        "namespace": "discussions"
      },
      "created_at": "2023-04-05T10:30:00Z"
    }
  ],
  "total": 2,
  "query_vector_id": "query-vector-id"
}
```

### Memory Batch Operations

Perform multiple memory operations in a single request.

**Endpoint:** `POST /api/memory/batch`

**Authentication Required:** Yes

**Request:**

```json
{
  "operations": [
    {
      "op": "store",
      "content": {
        "text": "First memory item",
        "type": "text/plain"
      },
      "context": {
        "project_id": "project-id",
        "scope": "project"
      }
    },
    {
      "op": "update",
      "id": "memory-id",
      "content": {
        "text": "Updated memory content",
        "type": "text/plain"
      }
    },
    {
      "op": "delete",
      "id": "memory-id-to-delete"
    }
  ]
}
```

**Response:**

```json
{
  "results": [
    {
      "status": "success",
      "op": "store",
      "id": "new-memory-id"
    },
    {
      "status": "success",
      "op": "update",
      "id": "memory-id"
    },
    {
      "status": "success",
      "op": "delete",
      "id": "memory-id-to-delete"
    }
  ],
  "success_count": 3,
  "error_count": 0
}
```

## Memory System Data Model

```
memory_items
├── id (uuid)
├── content (jsonb)
├── context (jsonb)
├── embedding (vector)
├── embedding_model (text)
├── embedding_status (enum)
├── tags (text[])
├── created_at (timestamp)
├── updated_at (timestamp)
├── expires_at (timestamp)
├── created_by (uuid)
├── tenant_id (uuid)
├── project_id (uuid)
└── scope (text)
```

## Memory Access Controls

The Memory System API enforces strict access controls:

1. **Project Scope**: Memory items are isolated by project
2. **Namespace Isolation**: Namespaces provide further isolation
3. **Permission Checks**: Access requires appropriate permissions
4. **Multi-tenancy**: Tenant isolation at the database level
5. **Audit Logging**: Memory access is logged for compliance

## Performance Considerations

The Memory System API is optimized for performance:

- **HNSW Indexes**: 3-6x faster vector search with pgvector
- **Connection Pooling**: Efficient database connections
- **Caching**: Frequently accessed memories are cached
- **Batch Processing**: Efficient bulk operations
- **Async Processing**: Non-blocking embedding generation

Performance metrics:
- p95 latency for retrieval: < 50ms
- p95 latency for search: < 100ms
- Maximum throughput: 1000+ requests/second

## Best Practices

1. **Use Batch Operations**: When working with multiple memory items, use batch operations for better performance.
2. **Include Proper TTL**: Always include appropriate TTL values to ensure memory items are automatically cleaned up.
3. **Use Contexts**: Properly scope memory items with contexts to improve organization and retrieval.
4. **Tag Everything**: Add meaningful tags to improve filtering and discovery.
5. **Handle Embedding Status**: Check embedding_status before performing vector searches, as some items may still be pending embedding generation.
6. **Optimize Query Size**: Keep query texts concise for better vector search performance.
7. **Use SDK Patterns**: The SDK provides optimized patterns for memory access.

## Rate Limits

- Store Operation: 10 requests per second per user
- Retrieve Operation: 20 requests per second per user
- Search Operation: 5 requests per second per user
- Batch Operation: 2 requests per second per user (max 50 operations per batch)

## Error Handling

Common error scenarios:

- **401 Unauthorized**: Missing or invalid authentication
- **403 Forbidden**: Insufficient permissions
- **404 Not Found**: Memory item not found
- **422 Unprocessable Entity**: Invalid request data
- **429 Too Many Requests**: Rate limit exceeded
- **500 Internal Server Error**: Server-side error

Always implement proper error handling in your applications to gracefully handle these scenarios.

## Next Steps

- [SDK Integration](/developer-guides/sdk-overview) - Learn how to use the SDK for memory access
- [Memory Permissions](/api-reference/permissions-api) - Learn about memory-specific permissions
- [Event System](/api-reference/event-system) - Subscribe to memory events 