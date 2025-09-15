# SocratesCode Development Guide

## Table of Contents
- [Development Workflow](#development-workflow)
- [Code Organization](#code-organization)
- [Branch Strategy](#branch-strategy)
- [Testing Strategy](#testing-strategy)
- [Code Standards](#code-standards)
- [API Development](#api-development)
- [Frontend Development](#frontend-development)
- [AI Service Development](#ai-service-development)
- [Debugging and Troubleshooting](#debugging-and-troubleshooting)
- [Contributing Guidelines](#contributing-guidelines)

## Development Workflow

### Getting Started

#### Prerequisites
- Node.js 22.x
- Python 3.12+
- Docker and Docker Compose
- Git

#### Initial Setup
```bash
# Clone repository
git clone https://github.com/SocratesCodes/socratescode.git
cd socratescode

# Install dependencies
npm run install:all

# Set up environment variables
cp ai/.env.example ai/.env
cp apps/backend/.env.example apps/backend/.env
cp apps/frontend/.env.local.example apps/frontend/.env.local

# Edit environment files with your values
# Generate JWT keys for backend
cd apps/backend
openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
openssl rsa -pubout -in private_key.pem -out public_key.pem
cd ../..
```

#### Development Commands
```bash
# Start all services in development mode
npm run dev

# Start services individually
npm run dev:backend    # Backend on port 5000
npm run dev:frontend   # Frontend on port 3000
npm run dev:ai         # AI service on port 8002

# Clean development environment
npm run dev:clean      # Clears AI state files

# Clean install (removes all node_modules and reinstalls)
npm run clean:install

# Run tests
npm run test           # AI service tests
```

### Development Environment URLs
- **Frontend**: http://localhost:3000
- **Backend API**: http://localhost:5000/api/health
- **API Documentation**: http://localhost:5000/api-docs
- **AI Service**: http://localhost:8002/health

## Code Organization

### Project Structure
```
socratescode/
├── ai/                          # AI Service (Python/FastAPI)
│   ├── customAgent/             # Multi-agent implementations
│   │   ├── GreetingAgent.py     # Input gatekeeper
│   │   ├── EvaluationAgent.py   # Skill assessment
│   │   ├── SocraticTutorAgent.py # Question generation
│   │   ├── ValidationAgent.py   # Response validation
│   │   └── EndNode.py           # Terminal node
│   ├── mem0_implement/          # Memory management
│   ├── utils/                   # Utilities and prompt builders
│   ├── test-script/            # Testing framework
│   ├── app_team.py             # Main FastAPI application
│   ├── ConversationManager.py  # Conversation state management
│   └── file_operations.py      # MinIO integration
├── apps/
│   ├── backend/                # Backend Service (Node.js/Express)
│   │   ├── src/
│   │   │   ├── routes/         # API route handlers
│   │   │   ├── services/       # Business logic layer
│   │   │   ├── middleware/     # Authentication & security
│   │   │   ├── db/            # Database connections
│   │   │   ├── ws/            # WebSocket server
│   │   │   └── utils/         # Utilities
│   │   └── package.json
│   ├── frontend/               # Frontend Service (Next.js/React)
│   │   ├── app/               # Next.js app directory
│   │   ├── components/        # React components
│   │   ├── hooks/            # Custom React hooks
│   │   ├── lib/              # Utilities
│   │   ├── types/            # TypeScript types
│   │   └── package.json
│   └── docs/                  # Documentation site
├── docs/                      # Project documentation
├── docker-compose.yml         # Development containers
└── package.json              # Root workspace configuration
```

### Modular Architecture Principles
- **Feature-based organization**: Group related functionality together
- **Separation of concerns**: Clear boundaries between services and layers
- **Single responsibility**: Each module has one clear purpose
- **Dependency injection**: Use configuration and environment variables
- **Error handling**: Consistent error handling across all services

## Branch Strategy

### Branch Types
1. **Main Branch** (`main`): Production-ready code
2. **Demo Branch** (`demo`): Testing and staging environment
3. **Feature Branches**: Individual development work
4. **Hotfix Branches**: Critical bug fixes

### Naming Conventions
```bash
# Feature branches
feature/user-authentication
feature/conversation-history
feature/ai-agent-improvements

# Bug fixes
fix/socket-connection-issue
fix/memory-leak-frontend

# Hotfixes
hotfix/security-vulnerability
hotfix/database-connection

# Personal development branches
john-doe-dev
sarah-smith-branch
```

### Development Process
1. **Create Feature Branch**: Branch from `main` or your personal branch
2. **Develop**: Write code, tests, and documentation
3. **Test**: Ensure all tests pass locally
4. **Pull Request**: Create PR with detailed description
5. **Code Review**: Get approval from team members
6. **Demo Testing**: Merge to `demo` branch for testing
7. **Production**: After demo testing, merge to `main`

### Git Workflow
```bash
# Create and switch to feature branch
git checkout -b feature/new-feature

# Make commits with descriptive messages
git add .
git commit -m "Add user preference validation logic"

# Push to remote
git push -u origin feature/new-feature

# Create pull request on GitHub
# After review and approval, merge via GitHub UI
```

## Testing Strategy

### AI Service Testing (Python)
```bash
# Run all AI service tests
cd ai
pipenv run pytest

# Run specific test file
pipenv run pytest test-script/agent_tests.py

# Run with coverage
pipenv run pytest --cov=. test-script/

# Run specific test
pipenv run pytest test-script/agent_tests.py::test_socratic_tutor_response
```

#### Test Structure (`ai/test-script/`)
```python
# agent_tests.py
import pytest
from customAgent.SocraticTutorAgent import SocraticTutorAgent
from customAgent.ValidationAgent import ValidationAgent

@pytest.fixture
def socratic_tutor():
    return SocraticTutorAgent(
        name="test_tutor",
        model_client=MockModelClient()
    )

def test_socratic_tutor_response(socratic_tutor):
    """Test that Socratic tutor generates appropriate questions"""
    response = socratic_tutor.generate_questions("What is a function?")
    assert "P1." in response
    assert "P2." in response
    assert "P3." in response

class MockModelClient:
    """Mock OpenAI client for testing"""
    def __init__(self):
        pass

    async def create_completion(self, messages):
        return MockResponse("Test response with P1, P2, P3 questions")
```

### Backend Testing (Node.js)
```bash
# Run backend tests
cd apps/backend
npm test

# Run specific test file
npm test -- messageService.test.js

# Run with coverage
npm run test:coverage

# Run integration tests
npm run test:integration
```

#### Test Example
```javascript
// test/messageService.test.js
import { describe, it, expect, beforeEach, afterEach } from 'vitest';
import { saveMessage, getMessagesByConversationId } from '../src/services/messageService.js';

describe('MessageService', () => {
  beforeEach(() => {
    // Set up test database
  });

  afterEach(() => {
    // Clean up test data
  });

  it('should save message with correct structure', async () => {
    const message = {
      content: 'Test message',
      conversation_id: 'test-conv-id',
      source: 'user'
    };

    const savedMessage = await saveMessage('test-user-id', message);

    expect(savedMessage).toBeDefined();
    expect(savedMessage.message.message).toBe('Test message');
    expect(savedMessage.source).toBe('user');
  });

  it('should retrieve messages by conversation ID', async () => {
    const messages = await getMessagesByConversationId('test-conv-id', 'test-user-id');

    expect(Array.isArray(messages)).toBe(true);
    expect(messages.length).toBeGreaterThan(0);
  });
});
```

### Frontend Testing (Next.js/React)
```bash
# Run frontend tests
cd apps/frontend
npm test

# Run Cypress end-to-end tests
npm run cypress:open

# Run component tests
npm run test:components
```

#### Component Test Example
```typescript
// components/chat/ChatInput.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import ChatInput from './ChatInput';

describe('ChatInput', () => {
  it('should call handleSend when send button is clicked', () => {
    const mockHandleSend = jest.fn();
    const mockHandleInputChange = jest.fn();

    render(
      <ChatInput
        input="test message"
        handleSend={mockHandleSend}
        handleInputChange={mockHandleInputChange}
        status="ready"
      />
    );

    const sendButton = screen.getByRole('button', { name: /send/i });
    fireEvent.click(sendButton);

    expect(mockHandleSend).toHaveBeenCalledTimes(1);
  });

  it('should disable send button when status is processing', () => {
    render(
      <ChatInput
        input="test"
        handleSend={jest.fn()}
        handleInputChange={jest.fn()}
        status="processing"
      />
    );

    const sendButton = screen.getByRole('button', { name: /send/i });
    expect(sendButton).toBeDisabled();
  });
});
```

## Code Standards

### TypeScript Configuration
```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "ES6"],
    "allowJs": true,
    "skipLibCheck": true,
    "strict": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "plugins": [
      {
        "name": "next"
      }
    ],
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### ESLint Configuration
```javascript
// eslint.config.mjs
import { dirname } from "path";
import { fileURLToPath } from "url";
import { FlatCompat } from "@eslint/eslintrc";

const __filename = fileURLToPath(import.meta.url);
const __dirname = dirname(__filename);

const compat = new FlatCompat({
  baseDirectory: __dirname,
});

const eslintConfig = [
  ...compat.extends("next/core-web-vitals", "next/typescript"),
  {
    rules: {
      "@typescript-eslint/no-explicit-any": "warn",
      "@typescript-eslint/no-unused-vars": "error",
      "prefer-const": "error",
      "no-var": "error"
    }
  }
];

export default eslintConfig;
```

### Python Code Standards
```python
# .flake8
[flake8]
max-line-length = 88
extend-ignore = E203, W503
exclude = .git,__pycache__,docs/source/conf.py,old,build,dist

# pyproject.toml (for Black formatter)
[tool.black]
line-length = 88
target-version = ['py312']
include = '\.pyi?$'
```

### Commit Message Standards
```
feat: add user preference persistence
fix: resolve socket connection timeout issue
docs: update API documentation
refactor: simplify agent workflow logic
test: add integration tests for message service
chore: update dependencies
```

## API Development

### Backend API Structure
```javascript
// Route structure example
// apps/backend/src/routes/messageRouter.js
import express from 'express';
import { body, query, validationResult } from 'express-validator';
import {
  getMessagesByConversationId,
  saveMessage
} from '../services/messageService.js';

const router = express.Router();

/**
 * @swagger
 * /api/messages:
 *   get:
 *     summary: Get messages by conversation ID
 *     parameters:
 *       - name: conversation_id
 *         in: query
 *         required: true
 *         schema:
 *           type: string
 *     responses:
 *       200:
 *         description: Messages retrieved successfully
 */
router.get('/',
  [
    query('conversation_id').isUUID().withMessage('Invalid conversation ID'),
    query('limit').optional().isInt({ min: 1, max: 100 }).withMessage('Limit must be between 1-100'),
    query('page').optional().isInt({ min: 1 }).withMessage('Page must be positive integer')
  ],
  async (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }

    try {
      const { conversation_id, limit = 50, page = 1 } = req.query;
      const messages = await getMessagesByConversationId(
        conversation_id,
        req.user.user_id,
        parseInt(limit),
        parseInt(page)
      );

      res.json({ messages, page: parseInt(page), limit: parseInt(limit) });
    } catch (error) {
      console.error('Error fetching messages:', error);
      res.status(500).json({ error: 'Internal server error' });
    }
  }
);

export default router;
```

### Service Layer Pattern
```javascript
// apps/backend/src/services/messageService.js
import supabase from '../db/supabase_sdk.js';
import HttpError from '../utils/HttpError.js';

export async function saveMessage(user_id, messageData) {
  // Validation
  if (!messageData?.content?.trim()) {
    throw new HttpError('Message content is required', 400);
  }

  if (!messageData.conversation_id) {
    throw new HttpError('Conversation ID is required', 400);
  }

  // Business logic
  const maxSequence = await getMaxSequenceNumber(messageData.conversation_id);
  const message_id = crypto.randomUUID();

  // Database operation
  const { data, error } = await supabase
    .from('messages')
    .insert({
      message_id,
      conversation_id: messageData.conversation_id,
      sender_id: user_id,
      message: { message: messageData.content },
      source: 'user',
      sequence: maxSequence ? maxSequence + 1 : 1,
    })
    .select()
    .single();

  if (error) {
    throw new HttpError('Failed to save message', 500);
  }

  // Update conversation timestamp
  await updateConversationTimestamp(messageData.conversation_id);

  return data;
}
```

### Error Handling
```javascript
// apps/backend/src/utils/HttpError.js
export default class HttpError extends Error {
  constructor(message, statusCode = 500) {
    super(message);
    this.statusCode = statusCode;
    this.name = 'HttpError';
    Error.captureStackTrace(this, HttpError);
  }
}

// Global error handler middleware
// apps/backend/src/middleware/errorHandler.js
export function errorHandler(error, req, res, next) {
  console.error(error.stack);

  if (error instanceof HttpError) {
    return res.status(error.statusCode).json({
      error: error.message,
      statusCode: error.statusCode
    });
  }

  // Default error response
  res.status(500).json({
    error: 'Internal server error',
    statusCode: 500
  });
}
```

## Frontend Development

### Component Structure
```typescript
// components/chat/ChatMessage.tsx
interface ChatMessageProps {
  message: ChatMessage;
  onFeedback?: (messageId: string, feedback: 'like' | 'dislike') => void;
  className?: string;
}

export default function ChatMessage({
  message,
  onFeedback,
  className
}: ChatMessageProps) {
  const isUser = message.source === 'user';

  return (
    <div className={cn(
      "flex w-full",
      isUser ? "justify-end" : "justify-start",
      className
    )}>
      <div className={cn(
        "max-w-[80%] rounded-lg px-4 py-2",
        isUser
          ? "bg-blue-500 text-white"
          : "bg-gray-100 text-gray-900"
      )}>
        <ReactMarkdown>{message.content}</ReactMarkdown>

        {!isUser && onFeedback && (
          <MessageFeedback
            messageId={message.id}
            onFeedback={onFeedback}
          />
        )}
      </div>
    </div>
  );
}
```

### Custom Hooks Pattern
```typescript
// hooks/useConversation.ts
import { useState, useEffect } from 'react';
import { ChatMessage } from '@/types/chat';

export function useConversation(conversationId: string) {
  const [messages, setMessages] = useState<ChatMessage[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    if (!conversationId) return;

    const fetchMessages = async () => {
      try {
        setLoading(true);
        const response = await fetch(
          `/api/messages?conversation_id=${conversationId}`
        );

        if (!response.ok) {
          throw new Error('Failed to fetch messages');
        }

        const data = await response.json();
        setMessages(data.messages || []);
        setError(null);
      } catch (err) {
        setError(err instanceof Error ? err.message : 'Unknown error');
      } finally {
        setLoading(false);
      }
    };

    fetchMessages();
  }, [conversationId]);

  const addMessage = (message: ChatMessage) => {
    setMessages(prev => [...prev, message]);
  };

  const clearMessages = () => {
    setMessages([]);
  };

  return {
    messages,
    loading,
    error,
    addMessage,
    clearMessages
  };
}
```

### State Management with Zustand
```typescript
// stores/useConversationStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface ConversationStore {
  conversations: Conversation[];
  currentConversationId: string | null;
  setCurrentConversation: (id: string) => void;
  addConversation: (conversation: Conversation) => void;
  updateConversation: (id: string, updates: Partial<Conversation>) => void;
  deleteConversation: (id: string) => void;
}

export const useConversationStore = create<ConversationStore>()(
  persist(
    (set, get) => ({
      conversations: [],
      currentConversationId: null,

      setCurrentConversation: (id) =>
        set({ currentConversationId: id }),

      addConversation: (conversation) =>
        set(state => ({
          conversations: [...state.conversations, conversation]
        })),

      updateConversation: (id, updates) =>
        set(state => ({
          conversations: state.conversations.map(conv =>
            conv.id === id ? { ...conv, ...updates } : conv
          )
        })),

      deleteConversation: (id) =>
        set(state => ({
          conversations: state.conversations.filter(conv => conv.id !== id)
        }))
    }),
    {
      name: 'conversation-store',
      partialize: (state) => ({
        conversations: state.conversations,
        currentConversationId: state.currentConversationId
      })
    }
  )
);
```

## AI Service Development

### Agent Development Pattern
```python
# customAgent/BaseAgent.py
from abc import ABC, abstractmethod
from typing import Sequence, Optional
from autogen_agentchat.agents import AssistantAgent
from autogen_agentchat.base import Response
from autogen_agentchat.messages import BaseChatMessage, TextMessage
from autogen_core import CancellationToken

class BaseCustomAgent(AssistantAgent, ABC):
    def __init__(
        self,
        name: str,
        model_client,
        system_message: str,
        description: str = "",
        **kwargs
    ):
        super().__init__(
            name=name,
            description=description,
            model_client=model_client,
            system_message=system_message,
            **kwargs
        )

    @abstractmethod
    async def process_message(self, message: str) -> str:
        """Process incoming message and return response"""
        pass

    async def on_messages(
        self,
        messages: Sequence[BaseChatMessage],
        cancellation_token: CancellationToken
    ) -> Response:
        if not messages:
            return TextMessage(content="No message to process.")

        # Custom processing logic
        latest_message = messages[-1].content if messages else ""
        processed_response = await self.process_message(latest_message)

        return TextMessage(content=processed_response)
```

### Memory Management
```python
# mem0_implement/MemoryManager.py
import os
from mem0 import Memory
from typing import Optional, List, Dict, Any

class MemoryManager:
    def __init__(self, config_path: Optional[str] = None):
        self.config_path = config_path or os.getenv('MEM0_CONFIG_PATH')
        self.memory = Memory()

    async def store_evaluation(
        self,
        user_id: str,
        conversation_id: str,
        evaluation: str,
        user_question: str
    ) -> bool:
        """Store evaluation in user's memory"""
        try:
            memory_data = {
                "user_id": user_id,
                "conversation_id": conversation_id,
                "evaluation": evaluation,
                "user_question": user_question,
                "timestamp": datetime.now().isoformat()
            }

            result = self.memory.add(
                messages=[evaluation],
                user_id=user_id,
                metadata=memory_data
            )

            return result is not None

        except Exception as e:
            print(f"Error storing evaluation: {e}")
            return False

    async def get_memories(
        self,
        user_id: str,
        conversation_id: Optional[str] = None,
        include_recent: bool = True
    ) -> str:
        """Retrieve user memories as context string"""
        try:
            search_filters = {"user_id": user_id}
            if conversation_id:
                search_filters["conversation_id"] = conversation_id

            memories = self.memory.search(
                query="user assessment and learning progress",
                user_id=user_id,
                limit=10
            )

            if not memories:
                return ""

            # Format memories as context
            context_lines = ["=== User Assessment History ==="]

            for memory in memories:
                if 'evaluation' in memory.get('metadata', {}):
                    evaluation = memory['metadata']['evaluation']
                    context_lines.append(f"Assessment: {evaluation}")

            context_lines.append("=== End of Assessment History ===")
            return "\n".join(context_lines)

        except Exception as e:
            print(f"Error retrieving memories: {e}")
            return ""
```

### Configuration Management
```python
# customAgent/config_loader.py
import yaml
import os
from typing import Dict, Any

class ConfigLoader:
    def __init__(self, config_path: str):
        self.config_path = config_path
        self.config = self._load_config()

    def _load_config(self) -> Dict[str, Any]:
        """Load configuration from YAML file"""
        try:
            with open(self.config_path, 'r') as file:
                config = yaml.safe_load(file)

            # Substitute environment variables
            return self._substitute_env_vars(config)

        except FileNotFoundError:
            raise FileNotFoundError(f"Configuration file not found: {self.config_path}")
        except yaml.YAMLError as e:
            raise ValueError(f"Invalid YAML configuration: {e}")

    def _substitute_env_vars(self, config: Any) -> Any:
        """Recursively substitute environment variables in config"""
        if isinstance(config, dict):
            return {k: self._substitute_env_vars(v) for k, v in config.items()}
        elif isinstance(config, list):
            return [self._substitute_env_vars(item) for item in config]
        elif isinstance(config, str) and config.startswith('${') and config.endswith('}'):
            env_var = config[2:-1]
            return os.getenv(env_var, config)
        else:
            return config

    def get_agent_config(self, agent_name: str) -> Dict[str, Any]:
        """Get configuration for specific agent"""
        if agent_name not in self.config:
            raise KeyError(f"Agent configuration not found: {agent_name}")

        return self.config[agent_name]
```

## Debugging and Troubleshooting

### Logging Configuration
```python
# utils/logging_config.py
import logging
import sys
from datetime import datetime

def setup_logging(level=logging.INFO):
    """Set up logging configuration"""

    # Create formatters
    formatter = logging.Formatter(
        '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
    )

    # Console handler
    console_handler = logging.StreamHandler(sys.stdout)
    console_handler.setFormatter(formatter)

    # File handler
    file_handler = logging.FileHandler(
        f'logs/app_{datetime.now().strftime("%Y%m%d")}.log'
    )
    file_handler.setFormatter(formatter)

    # Root logger
    logger = logging.getLogger()
    logger.setLevel(level)
    logger.addHandler(console_handler)
    logger.addHandler(file_handler)

    return logger

# Usage in modules
logger = logging.getLogger(__name__)
```

### Debug Utilities
```typescript
// lib/debug.ts
export const DEBUG_MODE = process.env.NODE_ENV === 'development';

export function debugLog(message: string, data?: any) {
  if (DEBUG_MODE) {
    console.log(`[DEBUG] ${message}`, data);
  }
}

export function debugError(message: string, error: Error) {
  if (DEBUG_MODE) {
    console.error(`[DEBUG ERROR] ${message}`, error);
  }
}

export function debugTime(label: string) {
  if (DEBUG_MODE) {
    console.time(label);
  }
}

export function debugTimeEnd(label: string) {
  if (DEBUG_MODE) {
    console.timeEnd(label);
  }
}
```

### Performance Monitoring
```javascript
// utils/performance.js
export class PerformanceMonitor {
  static timers = new Map();

  static start(label) {
    this.timers.set(label, performance.now());
  }

  static end(label) {
    const startTime = this.timers.get(label);
    if (startTime) {
      const duration = performance.now() - startTime;
      console.log(`[PERF] ${label}: ${duration.toFixed(2)}ms`);
      this.timers.delete(label);
      return duration;
    }
    return null;
  }

  static async measureAsync(label, asyncFn) {
    this.start(label);
    try {
      const result = await asyncFn();
      this.end(label);
      return result;
    } catch (error) {
      this.end(label);
      throw error;
    }
  }
}

// Usage
PerformanceMonitor.start('api-call');
await fetch('/api/messages');
PerformanceMonitor.end('api-call');

// Or with async wrapper
const messages = await PerformanceMonitor.measureAsync(
  'fetch-messages',
  () => fetch('/api/messages').then(r => r.json())
);
```

## Contributing Guidelines

### Pull Request Process
1. **Fork the repository** or create a feature branch
2. **Write clear, descriptive commit messages**
3. **Include tests** for new functionality
4. **Update documentation** as needed
5. **Follow code style** guidelines
6. **Test thoroughly** before submitting
7. **Create descriptive PR** with:
   - Clear title and description
   - List of changes made
   - Testing instructions
   - Screenshots (if UI changes)

### Code Review Checklist
- [ ] Code follows project style guidelines
- [ ] All tests pass
- [ ] New functionality includes tests
- [ ] Documentation is updated
- [ ] No console.log statements in production code
- [ ] Error handling is implemented
- [ ] Security considerations addressed
- [ ] Performance impact considered

### Issue Reporting
When reporting bugs, include:
- **Environment details** (OS, Node.js version, etc.)
- **Steps to reproduce** the issue
- **Expected behavior** vs actual behavior
- **Error messages** or logs
- **Screenshots** if applicable

### Feature Requests
For new features, provide:
- **Clear description** of the feature
- **Use case** and business value
- **Proposed implementation** approach
- **Alternative solutions** considered
- **Additional context** or mockups

This development guide provides comprehensive information for contributing to the SocratesCode project, from initial setup through advanced debugging techniques.
