---
title: 'Installation & Setup'
description: 'Get started with the Vibing AI development environment'
---

This guide walks you through setting up your development environment for building on the Vibing AI platform.

## Prerequisites

Before getting started, ensure you have the following installed:

- **Node.js**: Version 20.6.0 LTS or later
- **npm**: Version 9.0.0 or later
- **Git**: Latest version

You can check your installed versions with:

```bash
node --version
npm --version
git --version
```

## Developer Account Setup

### 1. Create a Developer Account

1. Go to [https://developers.vibing.im](https://developers.vibing.im)
2. Click "Sign Up" and complete the registration form
3. Verify your email address
4. Complete your developer profile

### 2. Create an Application

1. Log in to the [Developer Portal](https://developers.vibing.im)
2. Navigate to "Applications" and click "Create New Application"
3. Enter application details:
   - Name
   - Description
   - Category
   - Redirect URIs (for OAuth)
4. Note your Client ID and Client Secret

## Installing the SDK and CLI

### Install the SDK

The Vibing AI SDK provides the core libraries for developing platform offerings:

```bash
# Create a new project directory
mkdir my-vibing-project
cd my-vibing-project

# Initialize a new npm project
npm init -y

# Install the SDK
npm install @vibing-ai/sdk
```

### Install the CLI Tools

The Vibing CLI provides helpful commands for development, testing, and deployment:

```bash
# Install globally
npm install -g @vibing-ai/cli

# Verify installation
vibe --version
```

## Project Initialization

Initialize a new project using the CLI:

```bash
# For a new app
vibe init app my-awesome-app

# For a plugin
vibe init plugin my-productivity-plugin

# For an agent
vibe init agent my-specialized-agent

# For a theme
vibe init theme my-beautiful-theme

# For a studio
vibe init studio my-integrated-studio
```

This will create a new directory with the following structure:

```
my-awesome-app/
├── src/
│   ├── index.ts         # Main entry point
│   ├── components/      # UI components
│   ├── services/        # Business logic
│   └── utils/           # Helper functions
├── public/              # Static assets
├── tests/               # Test files
├── vibe.config.js       # Vibing AI configuration
├── package.json         # Project dependencies
├── tsconfig.json        # TypeScript configuration
└── README.md            # Project documentation
```

## Configuration

### Configure Your Project

Update the `vibe.config.js` file with your application details:

```javascript
module.exports = {
  name: 'My Awesome App',
  version: '1.0.0',
  description: 'Description of your application',
  author: 'Your Name',
  license: 'MIT',
  type: 'app', // or 'plugin', 'agent', 'theme', 'studio'
  surfaces: ['conversationCard', 'appTab'],
  permissions: [
    'memory:read:conversation;purpose=user_context;ttl=session',
    'memory:write:conversation;purpose=conversation_results;ttl=persistent'
  ],
  clientId: 'YOUR_CLIENT_ID', // From Developer Portal
  clientSecret: 'YOUR_CLIENT_SECRET', // From Developer Portal
  redirectUri: 'http://localhost:3000/callback'
};
```

### Set Up Environment Variables

Create a `.env` file to store sensitive information:

```
VIBE_CLIENT_ID=your_client_id
VIBE_CLIENT_SECRET=your_client_secret
VIBE_REDIRECT_URI=http://localhost:3000/callback
VIBE_ENV=development
```

Add `.env` to your `.gitignore` file to prevent committing sensitive information.

## Development Workflow

### Start Development Server

Run the development server to start working on your project:

```bash
# Start local development server
vibe dev

# Start with specific options
vibe dev --port 3001 --open
```

This will:
1. Start a local development server
2. Watch for file changes
3. Provide a sandbox environment
4. Open your default browser (with --open flag)

### Testing Your Project

Run tests to ensure your project is working correctly:

```bash
# Run all tests
vibe test

# Run specific test
vibe test tests/specific-test.ts

# Run with coverage
vibe test --coverage
```

### Building for Production

Build your project for production:

```bash
# Build project
vibe build

# Build with specific target
vibe build --target production
```

## Sandbox Environment

The sandbox environment allows you to test your offering without affecting production data:

```bash
# Initialize sandbox
vibe sandbox init

# Reset sandbox
vibe sandbox reset

# Import test data
vibe sandbox import data.json
```

## Troubleshooting

### Common Issues

1. **EACCES permissions errors**
   
   If you get permission errors when installing the CLI globally:
   ```bash
   npm install -g @vibing-ai/cli --unsafe-perm=true --allow-root
   ```

2. **Connection to developer portal fails**
   
   Check your internet connection and verify your API keys in `.env` file.

3. **Development server fails to start**
   
   Try clearing the cache:
   ```bash
   vibe cache clean
   ```

### Getting Help

If you encounter issues:

1. Check the [documentation](https://docs.vibing.im)
2. Join our [Discord community](https://discord.gg/vibing-ai)
3. Open an issue on [GitHub](https://github.com/vibing-ai)
4. Contact support at [support@vibing.ai](mailto:support@vibing.ai)

## Next Steps

Now that you have set up your development environment, you can:

- [Explore the SDK Overview](/developer-guides/sdk-overview)
- [Learn about App Development](/developer-guides/app-development)
- [Understand the Unified Memory System](/key-concepts/unified-memory-system)
- [Review the Integration Guide](/developer-experience-integration-guide) 