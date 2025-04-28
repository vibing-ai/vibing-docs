---
title: 'SDK Overview'
description: 'Introduction to the Vibing AI development SDK and tools'
---

# Vibing AI SDK Overview

The Vibing AI SDK provides a comprehensive set of tools, frameworks, and APIs for developing and integrating with the Vibing AI platform. This document provides an overview of the SDK components and capabilities.

## SDK Organization

The Vibing AI SDK is organized into logical modules for different aspects of platform development:

```
@vibing-ai/sdk/
├── app/             # App development framework
├── plugin/          # Plugin development utilities
├── agent/           # Agent development tools
├── theme/           # Theme development framework
├── studio/          # Studio composition tools
├── memory/          # Unified Memory System integration
├── permissions/     # Permission management
├── events/          # Event system integration
├── surfaces/        # Interface surface components
├── auth/            # Authentication utilities
├── ai/              # AI capabilities and integrations
└── utils/           # Common utilities and helpers
```

## Core Modules

### App Framework

Tools for building full-featured applications:

```typescript
import { createApp } from '@vibing-ai/sdk/app';

const myApp = createApp({
  name: 'My Application',
  version: '1.0.0',
  permissions: [
    'memory:read:project;purpose=content_analysis;ttl=session',
    'memory:write:project;purpose=save_user_data;ttl=session'
  ],
  surfaces: {
    app: AppComponent,
    conversation: ConversationComponent,
    canvas: CanvasComponent
  },
  onInstall: async () => {
    // Initialization logic
  },
  onUninstall: async () => {
    // Cleanup logic
  }
});
```

### Plugin Framework

System for creating lightweight extensions:

```typescript
import { createPlugin } from '@vibing-ai/sdk/plugin';
import { events } from '@vibing-ai/sdk/events';

const myPlugin = createPlugin({
  name: 'My Plugin',
  version: '1.0.0',
  permissions: [
    'memory:read:session;purpose=context_awareness;ttl=session'
  ],
  hooks: {
    'message:pre-send': async (message) => {
      // Modify message before sending
      return modifiedMessage;
    },
    'memory:post-write': async (memoryEvent) => {
      // React to memory changes
    }
  },
  commands: {
    'my-command': async (args) => {
      // Handle custom command
      return result;
    }
  }
});
```

### Agent Framework

Tools for building specialized AI assistants:

```typescript
import { createAgent } from '@vibing-ai/sdk/agent';
import { memory } from '@vibing-ai/sdk/memory';

const myAgent = createAgent({
  name: 'My Agent',
  version: '1.0.0',
  description: 'A specialized agent for specific tasks',
  capabilities: ['research', 'analysis', 'recommendation'],
  permissions: [
    'memory:read:project;purpose=domain_knowledge;ttl=session',
    'network:external:*.example.com;purpose=data_retrieval;ttl=request'
  ],
  handles: {
    // Intent handling based on user requests
    'research-topic': async (params, context) => {
      const { topic } = params;
      // Perform research on topic
      return researchResults;
    }
  },
  tools: {
    // Tools the agent can use
    'search-database': async (query) => {
      // Search implementation
      return searchResults;
    }
  }
});
```

### Theme Framework

Create custom visual themes for the platform:

```typescript
import { createTheme } from '@vibing-ai/sdk/theme';
import { colors, typography } from '@vibing-ai/block-kit/theming';

const myTheme = createTheme({
  name: 'Corporate Theme',
  styles: {
    colors: {
      primary: '#003366',
      secondary: '#ff9900',
      background: '#f5f7fa',
      text: '#333333'
    },
    typography: {
      fontFamily: '"Segoe UI", system-ui, sans-serif',
      baseSize: '16px'
    },
    spacing: {
      unit: '4px',
      scale: [1, 2, 3, 5, 8, 13]
    },
    // Additional theme settings
  }
});
```

### Studio Framework

Compose multiple offerings into integrated experiences:

```typescript
import { createStudio } from '@vibing-ai/sdk/studio';
import { myApp } from './my-app';
import { myPlugin } from './my-plugin';
import { myAgent } from './my-agent';
import { myTheme } from './my-theme';

const myStudio = createStudio({
  name: 'Integrated Workspace',
  description: 'Complete productivity environment',
  version: '1.0.0',
  components: [
    myApp,
    myPlugin,
    myAgent,
    myTheme
  ],
  configuration: {
    // Studio configuration
  }
});
```

## Platform Services

### Memory Integration

Interact with the Unified Memory System:

```typescript
import { memory } from '@vibing-ai/sdk/memory';

// Store data
await memory.set('user-preferences', { theme: 'dark' });

// Retrieve data
const preferences = await memory.get('user-preferences');

// Update data
await memory.update('user-preferences', { theme: 'light' });

// Delete data
await memory.delete('user-preferences');

// Query data
const results = await memory.query({
  type: 'message',
  filters: {
    conversation: 'current',
    timeRange: { start: '2023-01-01', end: '2023-01-31' }
  }
});
```

### Permission Management

Handle permission requests and checks:

```typescript
import { permissions } from '@vibing-ai/sdk/permissions';

// Request permissions
const granted = await permissions.request([
  'memory:read:conversation;purpose=user_context;ttl=session',
  'api:access:calendar;purpose=scheduling;ttl=persistent'
]);

// Check permissions
const hasPermission = await permissions.check('memory:write:global;purpose=user_preferences;ttl=persistent');
```

### Event System

Communicate between platform components:

```typescript
import { events } from '@vibing-ai/sdk/events';

// Subscribe to platform events
events.on('app:launch', (data) => {
  console.log('App launched');
});

// Emit custom events
events.emit('my-app:action-completed', { actionId: '12345' });

// Subscribe to custom events
events.on('my-app:action-completed', (data) => {
  console.log(`Action ${data.actionId} completed`);
});
```

### Authentication

Handle user authentication and session management:

```typescript
import { auth } from '@vibing-ai/sdk/auth';

// OAuth authentication
const authResponse = await auth.authenticate({
  method: 'oauth',
  clientId: 'your-client-id',
  scopes: ['read', 'write']
});

// API key authentication
auth.setApiKey('your-api-key');
```

## Development Tools

### CLI Tools

The Vibing AI CLI provides tools for development, testing, and deployment:

```bash
# Install the CLI
npm install -g @vibing-ai/cli

# Initialize a new app project
vibe init app my-awesome-app

# Start development server
vibe dev

# Run tests
vibe test

# Build for production
vibe build

# Deploy to the marketplace
vibe deploy
```

### Block Kit

UI component library for building consistent interfaces:

```typescript
import { 
  Card, 
  Button, 
  Text, 
  Input, 
  Checkbox, 
  Select, 
  Modal, 
  Tabs 
} from '@vibing-ai/block-kit';

// Layout components
import { 
  Stack, 
  Grid, 
  Container, 
  Divider 
} from '@vibing-ai/block-kit/layout';

// Usage example
const card = new Card({
  title: 'User Profile',
  content: [
    new Text('John Doe', { variant: 'heading' }),
    new Button('Edit Profile', { variant: 'primary' })
  ]
});
```

## Getting Started

To begin developing with the Vibing AI SDK:

1. Install the SDK and CLI tools:
   ```bash
   npm install @vibing-ai/sdk
   npm install -g @vibing-ai/cli
   ```

2. Initialize a new project:
   ```bash
   vibe init [type] [name]
   ```

3. Explore the SDK documentation for detailed guides on each component.

4. Use the CLI development tools to build, test, and deploy your offering.

## Next Steps

- [Installation and Setup](/developer-guides/installation-setup) - Detailed installation instructions
- [App Development Guide](/developer-guides/app-development) - Learn how to build full applications
- [Plugin Development Guide](/developer-guides/plugin-development) - Create platform extensions
- [Agent Development Guide](/developer-guides/agent-development) - Build specialized AI assistants
- [API Reference](/api-reference/introduction) - Detailed API documentation 