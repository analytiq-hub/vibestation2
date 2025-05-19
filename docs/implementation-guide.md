# Vibestation - Implementation Guide

This document provides practical guidance for implementing the Vibestation application based on the architectural design. It serves as a bridge between the high-level architecture and the actual code implementation.

## 1. Development Environment Setup

### 1.1. Prerequisites

- **Node.js**: v16.x or later
- **npm**: v8.x or later
- **Git**: Latest version
- **IDE**: Visual Studio Code (recommended with extensions for TypeScript, React, and Electron)
- **Operating System**: macOS, Windows, or Linux

### 1.2. Initial Setup

```bash
# Clone repository
git clone [repository-url] vibestation
cd vibestation

# Install dependencies
npm install

# Setup development environment
npm run setup-dev
```

### 1.3. Environment Variables

Create a `.env` file in the project root for development:

```
# AI API Keys
ANTHROPIC_API_KEY=your_anthropic_api_key
OPENAI_API_KEY=your_openai_api_key

# Stripe API Key (Test Mode)
STRIPE_API_KEY=your_stripe_test_api_key

# Development Settings
NODE_ENV=development
```

## 2. Project Structure

```
vibestation/
├── electron/                  # Electron main process code
│   ├── main.ts                # Main process entry point
│   ├── preload.ts             # Preload script for IPC
│   └── services/              # Main process services
│       ├── configService.ts   # Configuration handling
│       └── secureStorage.ts   # Secure API key storage
│
├── src/                       # Renderer process code (React app)
│   ├── components/            # React components
│   │   ├── App.tsx            # Root component
│   │   ├── ChatWindow.tsx     # Main chat interface
│   │   ├── HistorySidebar.tsx # Chat history sidebar
│   │   └── ArtifactWindow.tsx # HTML artifact display
│   │
│   ├── services/              # Application services
│   │   ├── aiService.ts       # AI model interaction
│   │   ├── mcpService.ts      # MCP tool management
│   │   └── chatHistoryService.ts # Chat history management
│   │
│   ├── mcp_tools/             # MCP tool implementations
│   │   └── stripeTool.ts      # Stripe MCP tool
│   │
│   ├── types/                 # TypeScript type definitions
│   ├── utils/                 # Utility functions
│   ├── index.tsx              # Renderer entry point
│   └── styles/                # CSS/Tailwind styles
│
├── config/                    # Configuration templates
│   ├── ai.config.json.template    # AI configuration template
│   └── mcp.config.json.template   # MCP configuration template
│
├── tests/                     # Test files
│   ├── unit/                  # Unit tests
│   ├── integration/           # Integration tests
│   └── mocks/                 # Test mocks
│
├── public/                    # Static assets
├── dist/                      # Build output
├── tailwind.config.js         # Tailwind CSS configuration
├── chakra.theme.ts            # Chakra UI theme configuration
├── tsconfig.json              # TypeScript configuration
├── package.json               # Project dependencies and scripts
├── jest.config.js             # Jest test configuration
└── .github/                   # GitHub Actions CI configuration
```

## 3. Key Implementation Guidelines

### 3.1. Main Process Implementation

1. **Secure API Key Storage**:
   - Use OS keychain integration with `keytar` library
   - Fall back to environment variables when secure storage is not available
   - Implement reference resolution of API keys in configuration files

2. **Configuration Loading**:
   - Load and parse `ai.config.json` and `mcp.config.json` from user data directory
   - Create default configurations from templates if not found
   - Validate configuration using schemas

3. **Chat History Persistence**:
   - Save and load chat history as JSON files in user data directory
   - Maintain an index file with session metadata
   - Support session operations: create, list, load, delete

4. **IPC Implementation**:
   - Use contextBridge to expose limited API to renderer process
   - Implement handlers for configuration, API key, and chat history operations
   - Validate all IPC inputs in main process

### 3.2. Renderer Process Implementation

1. **React Component Structure**:
   - App component as central state manager
   - ChatWindow for message display and input
   - HistorySidebar for chat session management
   - ArtifactWindow for HTML content display

2. **AI Integration**:
   - Use Vercel AI-SDK for AI model interaction
   - Support multiple providers (Anthropic, OpenAI)
   - Implement tool calling with Vercel AI-SDK

3. **MCP Tool Integration**:
   - Implement MCP tool for Stripe integration
   - Define tool schema compatible with Vercel AI-SDK
   - Handle tool execution and result formatting

4. **UI Implementation**:
   - Use Chakra UI as the primary component library
   - Integrate with Tailwind CSS for additional styling
   - Implement responsive design with Chakra UI's responsive props
   - Leverage Chakra UI's built-in color mode support for light and dark modes
   - Use Chakra UI's theming system for consistent design

5. **Chakra UI Integration**:
   - Implement Chakra UI provider at the application root
   - Create custom theme extending Chakra UI's default theme
   - Use Chakra UI components for all UI elements (buttons, inputs, modals, etc.)
   - Leverage Chakra UI hooks for color mode, media queries, and accessibility
   - Create custom compound components when needed for specialized UI requirements

## 4. Core Service Implementations

### 4.1. AI Service

The AI Service manages interactions with AI models:

- Load and parse AI configuration
- Initialize AI providers (Anthropic, OpenAI)
- Send user messages to AI models
- Process AI responses
- Handle tool calling requests

```typescript
// Key methods
initializeAI(): Promise<void>
sendMessage(content: string): Promise<void>
processTool(toolCall: ToolCall): Promise<ToolResult>
getMessages(): Message[]
```

### 4.2. MCP Service

The MCP Service manages MCP servers and tools:

- Load and parse MCP configuration
- Initialize MCP client
- Register MCP tools
- Format tool definitions for Vercel AI-SDK
- Execute tool calls

```typescript
// Key methods
initializeMCP(): Promise<void>
getToolDefinitions(): ToolDefinition[]
executeToolCall(toolName: string, action: string, params: any): Promise<any>
```

### 4.3. Stripe MCP Tool

The Stripe MCP Tool provides Stripe functionality:

- Initialize Stripe SDK with API key
- Implement tool actions:
  - List recent payments
  - Check account balance
  - Create payment links
- Format results for AI consumption

```typescript
// Key methods
initialize(apiKey: string): Promise<void>
listRecentPayments(limit?: number): Promise<any>
checkBalance(): Promise<any>
createPaymentLink(params: any): Promise<any>
```

### 4.4. Chat History Service

The Chat History Service manages chat persistence:

- Save chat sessions to local storage
- Load chat sessions from local storage
- List available chat sessions
- Create new chat sessions

```typescript
// Key methods
saveChatSession(sessionId: string, messages: Message[]): Promise<void>
loadChatSession(sessionId: string): Promise<Message[]>
listChatSessions(): Promise<ChatSession[]>
createChatSession(): Promise<string>
```

## 5. Configuration Files

### 5.1. AI Configuration Template

```json
{
  "providers": {
    "anthropic": {
      "apiKey": "${ANTHROPIC_API_KEY}",
      "models": {
        "default": "claude-3-sonnet-20240229",
        "available": [
          "claude-3-sonnet-20240229",
          "claude-3-opus-20240229"
        ]
      }
    },
    "openai": {
      "apiKey": "${OPENAI_API_KEY}",
      "models": {
        "default": "gpt-4o",
        "available": [
          "gpt-4o",
          "gpt-4-turbo"
        ]
      }
    },
    "default": "anthropic"
  }
}
```

### 5.2. MCP Configuration Template

```json
{
  "servers": [
    {
      "type": "stdio",
      "path": "./mcp_servers/stripe_server.js",
      "tools": ["stripe"]
    }
  ],
  "tools": {
    "stripe": {
      "apiKey": "${STRIPE_API_KEY}",
      "actions": ["listRecentPayments", "checkBalance", "createPaymentLink"]
    }
  }
}
```

## 6. Testing Strategy

### 6.1. Unit Testing

- Test individual components and services in isolation
- Mock external dependencies:
  - Vercel AI-SDK
  - MCP client
  - Stripe SDK
  - Electron APIs
  - Local storage

```typescript
// Example test for AI Service
describe('AIService', () => {
  let aiService: AIService;
  let mockMCPService: jest.Mocked<MCPService>;
  
  beforeEach(() => {
    mockMCPService = {
      initializeMCP: jest.fn(),
      getToolDefinitions: jest.fn().mockReturnValue([]),
      executeToolCall: jest.fn(),
      isInitialized: jest.fn().mockReturnValue(true)
    } as any;
    
    aiService = new AIService(mockMCPService);
  });
  
  it('should initialize with config', async () => {
    // Mock window.electronAPI
    global.window.electronAPI = {
      loadConfig: jest.fn().mockResolvedValue({
        providers: {
          anthropic: {
            apiKey: 'test-key',
            models: {
              default: 'claude-3-sonnet'
            }
          },
          default: 'anthropic'
        }
      })
    } as any;
    
    await aiService.initializeAI();
    expect(global.window.electronAPI.loadConfig).toHaveBeenCalledWith('ai');
  });
  
  // More tests...
});
```

### 6.2. Integration Testing

- Test interactions between services
- Test end-to-end flows
- Use mock servers for external dependencies

### 6.3. Test Coverage

- Aim for high code coverage (>80%)
- Focus on critical paths:
  - API key handling
  - Tool execution
  - Chat history persistence
  - Error handling

## 7. Deployment

### 7.1. Building for Production

```bash
# Build the application
npm run build

# Package for specific platform
npm run package:mac    # For macOS
npm run package:win    # For Windows
npm run package:linux  # For Linux
```

### 7.2. Distribution

- Code-sign the application for each platform
- Create installers
- Set up auto-update mechanism

## 8. Security Considerations

Refer to the Security Architecture document for detailed security guidelines. Key implementation points:

1. **Never hardcode API keys**
2. **Use secure storage for sensitive data**
3. **Validate all user inputs**
4. **Sanitize HTML content before display**
5. **Use proper Electron security settings**

## 9. Implementation Roadmap

1. **Phase 1**: Basic Electron shell and React setup
2. **Phase 2**: AI model connectivity
3. **Phase 3**: MCP client integration
4. **Phase 4**: Stripe MCP tool implementation
5. **Phase 5**: Chat history and UI refinements
6. **Phase 6**: Testing, refinement, and deployment

## 10. Resources

- [Electron Documentation](https://www.electronjs.org/docs)
- [React Documentation](https://reactjs.org/docs)
- [Vercel AI-SDK Documentation](https://sdk.vercel.ai/docs)
- [Stripe API Documentation](https://stripe.com/docs/api)
- [TypeScript Documentation](https://www.typescriptlang.org/docs)
- [Chakra UI Documentation](https://chakra-ui.com/docs/getting-started)

## 11. Chakra UI Dependencies

The application uses the following Chakra UI packages:

```json
{
  "dependencies": {
    "@chakra-ui/react": "^2.8.0",
    "@chakra-ui/icons": "^2.1.0",
    "@emotion/react": "^11.11.0",
    "@emotion/styled": "^11.11.0",
    "framer-motion": "^10.12.16"
  }
}
```

These packages provide:
- `@chakra-ui/react`: Core Chakra UI components and hooks
- `@chakra-ui/icons`: Icon set for Chakra UI
- `@emotion/react` and `@emotion/styled`: Required peer dependencies for Chakra UI's styling system
- `framer-motion`: Required peer dependency for Chakra UI's animations

## 12. Chakra UI Theme Configuration

Chakra UI uses a theme-based approach to styling. The application extends Chakra UI's default theme:

```typescript
// chakra.theme.ts
import { extendTheme, type ThemeConfig } from '@chakra-ui/react';

// Color mode config
const config: ThemeConfig = {
  initialColorMode: 'system',
  useSystemColorMode: true,
};

// Colors
const colors = {
  brand: {
    50: '#f0f9ff',
    100: '#e0f2fe',
    200: '#bae6fd',
    300: '#7dd3fc',
    400: '#38bdf8',
    500: '#0ea5e9',
    600: '#0284c7',
    700: '#0369a1',
    800: '#075985',
    900: '#0c4a6e',
  },
};

// Component style overrides
const components = {
  Button: {
    baseStyle: {
      fontWeight: 'semibold',
      borderRadius: 'md',
    },
    variants: {
      primary: {
        bg: 'brand.500',
        color: 'white',
        _hover: {
          bg: 'brand.600',
        },
      },
    },
  },
};

// Extend the theme
const theme = extendTheme({
  config,
  colors,
  components,
  fonts: {
    heading: 'Inter, system-ui, sans-serif',
    body: 'Inter, system-ui, sans-serif',
  },
});

export default theme;
```

This theme should be provided at the root of your application:

```typescript
// src/index.tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { ChakraProvider, ColorModeScript } from '@chakra-ui/react';
import App from './components/App';
import theme from './chakra.theme';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <ColorModeScript initialColorMode={theme.config.initialColorMode} />
    <ChakraProvider theme={theme}>
      <App />
    </ChakraProvider>
  </React.StrictMode>
);
```

## 13. Integrating Chakra UI with Tailwind CSS

While Chakra UI provides a comprehensive styling system, Tailwind CSS is also part of the technology stack. Here's how to integrate them:

### 13.1. Configuration Setup

Configure Tailwind to work alongside Chakra UI by adjusting the `tailwind.config.js`:

```javascript
// tailwind.config.js
module.exports = {
  content: ['./src/**/*.{js,jsx,ts,tsx}'],
  important: '#root', // Ensures Tailwind styles can override Chakra when needed
  theme: {
    extend: {
      // Extend Tailwind with custom utilities not provided by Chakra
    },
  },
  plugins: [],
  // Disable certain Tailwind features that conflict with Chakra
  corePlugins: {
    preflight: false, // Disable Tailwind's base styles
  },
};
```

### 13.2. Usage Strategy

- **Primary Component Structure**: Use Chakra UI components for layout and interactive elements
- **Custom Styling**: Apply Tailwind classes for custom styling needs or specific visual effects
- **Priority Order**: Chakra UI's prop-based styles take precedence over Tailwind classes

### 13.3. Example Component with Both Systems

```typescript
import { Box, Button } from '@chakra-ui/react';

// Component using both Chakra UI props and Tailwind classes
const ExampleComponent = () => {
  return (
    <Box
      p={4} // Chakra UI padding
      bg="white" // Chakra UI background
      className="shadow-lg hover:shadow-xl transition-shadow" // Tailwind classes
    >
      <Button
        colorScheme="brand" // Chakra UI color scheme
        className="backdrop-blur-sm" // Tailwind backdrop filter
      >
        Submit
      </Button>
    </Box>
  );
};
```

This approach leverages Chakra UI's component system while allowing for Tailwind's utility classes when needed for specific effects or customizations.