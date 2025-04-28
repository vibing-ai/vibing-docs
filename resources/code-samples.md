---
title: 'Code Samples'
description: 'Practical examples of common Vibing AI implementation patterns'
---

# Code Samples

This page provides reusable code samples for common Vibing AI implementation patterns. Use these examples as starting points for your own applications.

## Memory Operations

### Storing and Retrieving Structured Data

```typescript
import { VibingAI } from '@vibing/sdk';

// Initialize the client
const vibingClient = new VibingAI({
  apiKey: process.env.VIBING_API_KEY,
});

// Store structured data with metadata and tags
async function storeCustomerPreference(
  customerId: string, 
  preference: { category: string, value: any }
) {
  await vibingClient.memory.store({
    content: {
      type: 'application/json',
      data: preference
    },
    context: {
      customer_id: customerId,
      timestamp: new Date().toISOString(),
      source: 'customer_profile'
    },
    tags: ['customer_preference', preference.category]
  });
}

// Retrieve data with exact filtering and semantic search
async function getRelevantPreferences(customerId: string, query: string) {
  return await vibingClient.memory.searchText({
    text: query,
    filters: {
      'context.customer_id': customerId,
      tags: ['customer_preference']
    },
    limit: 10
  });
}
```

### Working with Vector Embeddings

```typescript
import { VibingAI } from '@vibing/sdk';

const vibingClient = new VibingAI({
  apiKey: process.env.VIBING_API_KEY,
});

// Store with custom embedding
async function storeWithCustomEmbedding(document: string, embedding: number[]) {
  return await vibingClient.memory.store({
    content: {
      type: 'text/plain',
      text: document
    },
    embedding: embedding,
    context: {
      source: 'custom_embedding_service'
    }
  });
}

// Search using vector similarity
async function findSimilarDocuments(embedding: number[]) {
  return await vibingClient.memory.searchVector({
    vector: embedding,
    limit: 5
  });
}
```

## Event Handling

### Creating an Event-Driven Chatbot

```typescript
import { VibingAI } from '@vibing/sdk';

const vibingClient = new VibingAI({
  apiKey: process.env.VIBING_API_KEY,
});

// Define event handlers
vibingClient.events.on('conversation.started', async (event) => {
  const { conversation_id } = event.payload;
  
  // Send welcome message
  await vibingClient.messages.send({
    conversation_id,
    content: 'Welcome! How can I assist you today?',
    sender: 'assistant'
  });
  
  // Log conversation start
  console.log(`New conversation started: ${conversation_id}`);
});

vibingClient.events.on('conversation.message_received', async (event) => {
  const { conversation_id, message } = event.payload;
  
  // Process incoming message
  if (message.content.includes('help')) {
    await vibingClient.messages.send({
      conversation_id,
      content: 'Here are some commands you can use...',
      sender: 'assistant'
    });
  } else {
    // Process with AI
    await processWithAI(conversation_id, message);
  }
});

// Start listening for events
vibingClient.events.listen();
```

### Implementing a Multi-Step Workflow

```typescript
import { VibingAI } from '@vibing/sdk';

const vibingClient = new VibingAI({
  apiKey: process.env.VIBING_API_KEY,
});

// Define workflow stages
const orderWorkflow = vibingClient.workflows.create({
  name: 'order_processing',
  steps: [
    {
      id: 'collect_order_details',
      handler: async (context, { proceed }) => {
        const { conversation_id, user_id } = context;
        // Collect and validate order details
        const orderDetails = await collectOrderDetails(conversation_id);
        
        // Store order details in memory
        await vibingClient.memory.store({
          content: {
            type: 'application/json',
            data: orderDetails
          },
          context: {
            user_id,
            workflow: 'order_processing',
            stage: 'details'
          },
          tags: ['order', 'details']
        });
        
        // Proceed to next step
        return proceed({ orderDetails });
      }
    },
    {
      id: 'process_payment',
      handler: async (context, { proceed, fail }) => {
        const { orderDetails } = context;
        try {
          // Process payment
          const paymentResult = await processPayment(orderDetails);
          return proceed({ paymentResult });
        } catch (error) {
          return fail({ error: error.message });
        }
      }
    },
    {
      id: 'confirm_order',
      handler: async (context) => {
        const { conversation_id, orderDetails, paymentResult } = context;
        
        // Send confirmation message
        await vibingClient.messages.send({
          conversation_id,
          content: `Your order has been confirmed! Order #${paymentResult.orderId}`,
          sender: 'assistant'
        });
        
        // Notify fulfillment system
        await notifyFulfillment(orderDetails, paymentResult);
        
        return { status: 'completed', orderId: paymentResult.orderId };
      }
    }
  ],
  onError: async (error, context) => {
    const { conversation_id } = context;
    await vibingClient.messages.send({
      conversation_id,
      content: `There was an error processing your order: ${error.message}`,
      sender: 'assistant'
    });
    
    // Log error for support team
    console.error('Order workflow error:', error, context);
  }
});

// Trigger workflow
async function startOrderProcess(conversation_id: string, user_id: string) {
  return await orderWorkflow.start({
    conversation_id,
    user_id
  });
}
```

## UI Components

### Creating a Custom Chat Interface

```tsx
import React, { useState, useEffect } from 'react';
import { VibingAI, Message } from '@vibing/react';

const CustomChatInterface = ({ conversationId }) => {
  const [messages, setMessages] = useState<Message[]>([]);
  const [inputText, setInputText] = useState('');
  const vibingClient = VibingAI.useClient();
  
  // Load conversation history
  useEffect(() => {
    async function loadHistory() {
      const history = await vibingClient.messages.list({
        conversation_id: conversationId,
        limit: 50
      });
      setMessages(history.data);
    }
    
    loadHistory();
    
    // Set up real-time updates
    const unsubscribe = vibingClient.events.on(
      'conversation.message_received',
      (event) => {
        if (event.payload.conversation_id === conversationId) {
          setMessages(prev => [...prev, event.payload.message]);
        }
      }
    );
    
    return () => unsubscribe();
  }, [conversationId]);
  
  // Send message
  const sendMessage = async () => {
    if (!inputText.trim()) return;
    
    // Optimistic update
    const tempId = `temp-${Date.now()}`;
    const tempMessage = {
      id: tempId,
      conversation_id: conversationId,
      content: inputText,
      sender: 'user',
      created_at: new Date().toISOString()
    };
    
    setMessages(prev => [...prev, tempMessage]);
    setInputText('');
    
    // Send to API
    try {
      await vibingClient.messages.send({
        conversation_id: conversationId,
        content: inputText,
        sender: 'user'
      });
    } catch (error) {
      console.error('Failed to send message:', error);
      // Remove temp message and show error
      setMessages(prev => prev.filter(m => m.id !== tempId));
      alert('Failed to send message. Please try again.');
    }
  };
  
  return (
    <div className="chat-container">
      <div className="message-list">
        {messages.map(message => (
          <div 
            key={message.id} 
            className={`message ${message.sender === 'user' ? 'user' : 'assistant'}`}
          >
            <div className="message-content">{message.content}</div>
            <div className="message-time">
              {new Date(message.created_at).toLocaleTimeString()}
            </div>
          </div>
        ))}
      </div>
      
      <div className="input-area">
        <textarea
          value={inputText}
          onChange={(e) => setInputText(e.target.value)}
          placeholder="Type your message..."
          onKeyDown={(e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
              e.preventDefault();
              sendMessage();
            }
          }}
        />
        <button onClick={sendMessage}>Send</button>
      </div>
    </div>
  );
};

export default CustomChatInterface;
```

### Building a Feedback Collection Component

```tsx
import React, { useState } from 'react';
import { VibingAI } from '@vibing/react';

const FeedbackComponent = ({ messageId, conversationId }) => {
  const [feedback, setFeedback] = useState<null | 'positive' | 'negative'>(null);
  const [comment, setComment] = useState('');
  const [isSubmitting, setIsSubmitting] = useState(false);
  const [isSubmitted, setIsSubmitted] = useState(false);
  
  const vibingClient = VibingAI.useClient();
  
  const submitFeedback = async () => {
    if (!feedback) return;
    
    setIsSubmitting(true);
    
    try {
      await vibingClient.feedback.create({
        message_id: messageId,
        conversation_id: conversationId,
        rating: feedback === 'positive' ? 1 : -1,
        comment: comment || undefined
      });
      
      // Store in memory for analytics and improvement
      await vibingClient.memory.store({
        content: {
          type: 'application/json',
          data: {
            message_id: messageId,
            rating: feedback === 'positive' ? 1 : -1,
            comment
          }
        },
        context: {
          conversation_id: conversationId,
          timestamp: new Date().toISOString()
        },
        tags: ['feedback', feedback]
      });
      
      setIsSubmitted(true);
    } catch (error) {
      console.error('Failed to submit feedback:', error);
      alert('Failed to submit feedback. Please try again.');
    } finally {
      setIsSubmitting(false);
    }
  };
  
  if (isSubmitted) {
    return <div className="feedback-thanks">Thank you for your feedback!</div>;
  }
  
  return (
    <div className="feedback-container">
      <div className="feedback-buttons">
        <button 
          className={feedback === 'positive' ? 'active' : ''}
          onClick={() => setFeedback('positive')}
          aria-label="Positive feedback"
        >
          👍
        </button>
        <button 
          className={feedback === 'negative' ? 'active' : ''}
          onClick={() => setFeedback('negative')}
          aria-label="Negative feedback"
        >
          👎
        </button>
      </div>
      
      {feedback && (
        <>
          <textarea
            value={comment}
            onChange={(e) => setComment(e.target.value)}
            placeholder={feedback === 'positive' 
              ? "What did you like about this response?" 
              : "How could this response be improved?"}
          />
          <button 
            onClick={submitFeedback}
            disabled={isSubmitting}
          >
            {isSubmitting ? 'Submitting...' : 'Submit Feedback'}
          </button>
        </>
      )}
    </div>
  );
};

export default FeedbackComponent;
```

## Integration Patterns

### CRM Integration

```typescript
import { VibingAI } from '@vibing/sdk';
import { Salesforce } from '@salesforce/api';

const vibingClient = new VibingAI({
  apiKey: process.env.VIBING_API_KEY,
});

const salesforce = new Salesforce({
  clientId: process.env.SALESFORCE_CLIENT_ID,
  clientSecret: process.env.SALESFORCE_CLIENT_SECRET,
  username: process.env.SALESFORCE_USERNAME,
  password: process.env.SALESFORCE_PASSWORD,
});

// Sync contact data from CRM to Vibing memory
async function syncContactToMemory(contactId: string) {
  // Fetch contact from Salesforce
  const contact = await salesforce.sobject('Contact').retrieve(contactId);
  
  // Store in Vibing memory
  await vibingClient.memory.store({
    content: {
      type: 'application/json',
      data: {
        name: `${contact.FirstName} ${contact.LastName}`,
        email: contact.Email,
        phone: contact.Phone,
        company: contact.Account?.Name,
        lastContactDate: contact.LastActivityDate
      }
    },
    context: {
      source: 'salesforce',
      contact_id: contactId,
      account_id: contact.AccountId
    },
    tags: ['contact', 'crm']
  });
  
  // Log sync
  console.log(`Synced contact ${contactId} to Vibing memory`);
}

// Create CRM task from conversation
async function createTaskFromConversation(conversationId: string, contactId: string) {
  // Get recent messages
  const messages = await vibingClient.messages.list({
    conversation_id: conversationId,
    limit: 10
  });
  
  // Create task in Salesforce
  const task = await salesforce.sobject('Task').create({
    Subject: 'Follow up from AI conversation',
    Description: messages.data.map(m => `${m.sender}: ${m.content}`).join('\n'),
    WhoId: contactId,
    Status: 'Not Started',
    Priority: 'Normal',
    ActivityDate: new Date(Date.now() + 86400000) // Tomorrow
  });
  
  return task;
}
```

### Authentication with OAuth

```typescript
import express from 'express';
import { VibingAI } from '@vibing/sdk';
import passport from 'passport';
import { Strategy as GoogleStrategy } from 'passport-google-oauth20';

const app = express();
const PORT = process.env.PORT || 3000;

// Initialize Vibing client
const vibingClient = new VibingAI({
  apiKey: process.env.VIBING_API_KEY,
});

// Set up Passport
passport.use(new GoogleStrategy({
  clientID: process.env.GOOGLE_CLIENT_ID,
  clientSecret: process.env.GOOGLE_CLIENT_SECRET,
  callbackURL: '/auth/google/callback'
}, async (accessToken, refreshToken, profile, done) => {
  try {
    // Store user info in Vibing memory
    const memoryItem = await vibingClient.memory.store({
      content: {
        type: 'application/json',
        data: {
          provider: 'google',
          profile_id: profile.id,
          display_name: profile.displayName,
          email: profile.emails?.[0]?.value,
          picture: profile.photos?.[0]?.value,
          access_token: accessToken,
          refresh_token: refreshToken
        }
      },
      context: {
        user_id: profile.id,
        auth_provider: 'google'
      },
      tags: ['user', 'auth', 'google']
    });
    
    done(null, { 
      id: profile.id, 
      memory_id: memoryItem.id 
    });
  } catch (error) {
    done(error);
  }
}));

passport.serializeUser((user, done) => {
  done(null, user.id);
});

passport.deserializeUser(async (id, done) => {
  try {
    // Retrieve user from Vibing memory
    const results = await vibingClient.memory.search({
      filters: {
        'context.user_id': id,
        'context.auth_provider': 'google'
      },
      limit: 1
    });
    
    if (results.data.length === 0) {
      return done(new Error('User not found'));
    }
    
    const userData = results.data[0].content.data;
    done(null, userData);
  } catch (error) {
    done(error);
  }
});

// Express middleware
app.use(express.json());
app.use(require('express-session')({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false
}));
app.use(passport.initialize());
app.use(passport.session());

// Auth routes
app.get('/auth/google',
  passport.authenticate('google', { scope: ['profile', 'email'] })
);

app.get('/auth/google/callback',
  passport.authenticate('google', { failureRedirect: '/login' }),
  (req, res) => {
    res.redirect('/dashboard');
  }
);

// Protected route example
app.get('/dashboard', ensureAuthenticated, (req, res) => {
  res.json({ user: req.user });
});

function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
}

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

## Data Processing

### Document Processing Pipeline

```typescript
import { VibingAI } from '@vibing/sdk';
import { PDFExtract } from 'pdf.js-extract';
import { v4 as uuidv4 } from 'uuid';

const vibingClient = new VibingAI({
  apiKey: process.env.VIBING_API_KEY,
});
const pdfExtract = new PDFExtract();

// Process and store a PDF document
async function processPdfDocument(filePath: string, metadata: Record<string, any>) {
  // Extract text from PDF
  const result = await pdfExtract.extract(filePath);
  
  // Generate document ID
  const documentId = uuidv4();
  
  // Process each page
  for (let i = 0; i < result.pages.length; i++) {
    const page = result.pages[i];
    const pageText = page.content.map(item => item.str).join(' ');
    
    // Store page content
    await vibingClient.memory.store({
      content: {
        type: 'text/plain',
        text: pageText
      },
      context: {
        document_id: documentId,
        page_number: i + 1,
        total_pages: result.pages.length,
        ...metadata
      },
      tags: ['document', 'pdf', 'page']
    });
  }
  
  // Store document metadata
  await vibingClient.memory.store({
    content: {
      type: 'application/json',
      data: {
        filename: filePath.split('/').pop(),
        page_count: result.pages.length,
        processed_at: new Date().toISOString(),
        ...metadata
      }
    },
    context: {
      document_id: documentId
    },
    tags: ['document', 'pdf', 'metadata']
  });
  
  return {
    document_id: documentId,
    page_count: result.pages.length
  };
}

// Search within processed documents
async function searchDocuments(query: string, filters: Record<string, any> = {}) {
  return await vibingClient.memory.searchText({
    text: query,
    filters: {
      tags: ['document', 'pdf', 'page'],
      ...filters
    },
    limit: 10
  });
}
```

### Streaming Data Processing

```typescript
import { VibingAI } from '@vibing/sdk';
import { TwitterApi } from 'twitter-api-v2';

const vibingClient = new VibingAI({
  apiKey: process.env.VIBING_API_KEY,
});

const twitterClient = new TwitterApi({
  appKey: process.env.TWITTER_API_KEY,
  appSecret: process.env.TWITTER_API_SECRET,
  accessToken: process.env.TWITTER_ACCESS_TOKEN,
  accessSecret: process.env.TWITTER_ACCESS_SECRET,
});

// Process and analyze tweets in real-time
async function monitorTwitterKeywords(keywords: string[]) {
  const stream = await twitterClient.v2.searchStream({
    'tweet.fields': ['created_at', 'public_metrics', 'author_id', 'lang'],
    'user.fields': ['name', 'username', 'profile_image_url'],
    'expansions': ['author_id']
  });
  
  // Process each tweet
  stream.on('data', async tweet => {
    // Check if tweet contains our keywords
    const tweetText = tweet.data.text.toLowerCase();
    const matchedKeywords = keywords.filter(keyword => 
      tweetText.includes(keyword.toLowerCase())
    );
    
    if (matchedKeywords.length > 0) {
      // Get user info
      const user = tweet.includes.users.find(u => u.id === tweet.data.author_id);
      
      // Store tweet in memory
      await vibingClient.memory.store({
        content: {
          type: 'text/plain',
          text: tweet.data.text
        },
        context: {
          tweet_id: tweet.data.id,
          user_id: tweet.data.author_id,
          username: user?.username,
          created_at: tweet.data.created_at,
          keywords: matchedKeywords,
          metrics: tweet.data.public_metrics
        },
        tags: ['tweet', 'social_media', ...matchedKeywords]
      });
      
      // Perform sentiment analysis
      const sentiment = await analyzeSentiment(tweet.data.text);
      
      // Store analysis results
      await vibingClient.memory.store({
        content: {
          type: 'application/json',
          data: {
            sentiment: sentiment.score,
            entities: sentiment.entities,
            keywords: matchedKeywords
          }
        },
        context: {
          tweet_id: tweet.data.id,
          analysis_type: 'sentiment'
        },
        tags: ['analysis', 'sentiment', 'tweet']
      });
      
      console.log(`Processed tweet: ${tweet.data.id}`);
    }
  });
  
  // Handle errors
  stream.on('error', error => {
    console.error('Twitter stream error:', error);
  });
  
  return stream;
}

// Simple sentiment analysis function
async function analyzeSentiment(text: string) {
  // In a real application, you would use a sentiment analysis service
  // or the Vibing AI capabilities for this
  return {
    score: Math.random() * 2 - 1, // -1 to 1
    entities: []
  };
}
```

## Next Steps

- [Best Practices](/resources/best-practices) - Learn recommended approaches
- [Use Cases](/resources/use-cases) - See how these patterns apply to real scenarios
- [API Reference](/api-reference/overview) - Explore the complete API documentation 