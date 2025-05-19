# Vibestation - Data Flow Diagrams

This document illustrates the key data flows within the Vibestation application, showing how information moves between components and external systems.

## 1. Core User Interaction Flow

```mermaid
flowchart TD
    User([User]) -->|Enters message| InputField[Chat Input Field]
    InputField -->|Submit message| ChatWindow[Chat Window Component]
    ChatWindow -->|sendMessage| AIService[AI Service]
    AIService -->|useChat.append| VercelAISDK[Vercel AI-SDK]
    VercelAISDK -->|API Request| AIModel[AI Model API]
    AIModel -->|API Response| VercelAISDK
    VercelAISDK -->|Response Event| AIService
    AIService -->|Message Update| ChatWindow
    ChatWindow -->|Display message| MessageList[Message List UI]
    MessageList -->|Visible to| User
    
    AIService -->|saveChatSession| ChatHistoryService[Chat History Service]
    ChatHistoryService -->|Store| LocalStorage[(Local Storage)]
```

## 2. Tool Execution Flow

```mermaid
flowchart TD
    AIModel[AI Model API] -->|Tool Call Request| VercelAISDK[Vercel AI-SDK]
    VercelAISDK -->|Tool Call Event| AIService[AI Service]
    AIService -->|executeToolCall| MCPService[MCP Service]
    MCPService -->|Identify Tool| ToolRegistry[(Tool Registry)]
    ToolRegistry -->|Tool Implementation| MCPService
    MCPService -->|callTool| MCPClient[MCP Client]
    
    subgraph MCP_Servers [MCP Servers]
        LocalServer[Local STDIO Server]
        RemoteServer[Remote SSE Server]
    end
    
    MCPClient -->|Execute Tool| MCP_Servers
    MCP_Servers -->|Tool Result| MCPClient
    MCPClient -->|Tool Result| MCPService
    MCPService -->|Format Result| AIService
    AIService -->|Submit Tool Result| VercelAISDK
    VercelAISDK -->|Continue with Result| AIModel
    
    AIModel -->|Final Response| VercelAISDK
    VercelAISDK -->|Response Event| AIService
    AIService -->|Message Update| ChatWindow[Chat Window Component]
    ChatWindow -->|Display Tool Result| MessageList[Message List UI]
    MessageList -->|Visible to| User([User])
```

## 3. Stripe Tool Data Flow

```mermaid
flowchart TD
    AIModel[AI Model API] -->|Stripe Tool Call| VercelAISDK[Vercel AI-SDK]
    VercelAISDK -->|Tool Call Event| AIService[AI Service]
    AIService -->|executeToolCall 'stripe'| MCPService[MCP Service]
    MCPService -->|callTool 'stripe'| StripeTool[Stripe MCP Tool]
    
    subgraph SecureStorage [Secure Storage]
        APIKeyRetrieval[API Key Retrieval]
    end
    
    StripeTool -->|getAPIKey| SecureStorage
    SecureStorage -->|stripeApiKey| StripeTool
    
    StripeTool -->|API Request| StripeAPI[Stripe API]
    StripeAPI -->|API Response| StripeTool
    
    StripeTool -->|Format Result| MCPService
    MCPService -->|Tool Result| AIService
    AIService -->|Submit Tool Result| VercelAISDK
    
    %% HTML Artifact Display Flow
    StripeTool -->|Generate HTML| HTMLContent[HTML Content]
    AIService -->|HTML Content| ArtifactWindow[Artifact Window]
    ArtifactWindow -->|Display| SandboxedIframe[Sandboxed iframe]
    SandboxedIframe -->|Visible to| User([User])
```

## 4. Chat History Data Flow

```mermaid
flowchart TD
    %% Save Flow
    ChatWindow[Chat Window Component] -->|Current Messages| AIService[AI Service]
    AIService -->|saveChatSession| ChatHistoryService[Chat History Service]
    ChatHistoryService -->|Serialize| MessageData[Message Data]
    MessageData -->|Store| LocalStorage[(Local Storage)]
    
    %% Load Flow
    HistorySidebar[History Sidebar] -->|List Request| ChatHistoryService
    ChatHistoryService -->|Query Storage| LocalStorage
    LocalStorage -->|Session List| ChatHistoryService
    ChatHistoryService -->|Format Sessions| SessionList[Session List Data]
    SessionList -->|Display| HistorySidebar
    
    %% Select Session Flow
    User([User]) -->|Select Session| HistorySidebar
    HistorySidebar -->|loadSession| ChatHistoryService
    ChatHistoryService -->|Retrieve| LocalStorage
    LocalStorage -->|Session Data| ChatHistoryService
    ChatHistoryService -->|Deserialize| MessageData
    MessageData -->|setMessages| AIService
    AIService -->|Update UI| ChatWindow
    ChatWindow -->|Display Messages| User
```

## 5. Configuration Data Flow

```mermaid
flowchart TD
    %% Application Startup Flow
    AppStartup[Application Startup] -->|Initialize| ConfigService[Configuration Service]
    
    subgraph FileSystem [File System]
        AIConfigFile[ai.config.json]
        MCPConfigFile[mcp.config.json]
    end
    
    ConfigService -->|Read| FileSystem
    FileSystem -->|Config Data| ConfigService
    
    ConfigService -->|Validate| ValidationSchema[Schema Validation]
    ValidationSchema -->|Validation Result| ConfigService
    
    ConfigService -->|AIConfig| AIService[AI Service]
    ConfigService -->|MCPConfig| MCPService[MCP Service]
    
    %% API Key Flow
    subgraph SecureStorage [Secure Storage]
        OSKeychain[OS Keychain]
        EnvVars[Environment Variables]
    end
    
    ConfigService -->|Retrieve API Keys| SecureStorage
    SecureStorage -->|API Keys| ConfigService
    
    ConfigService -->|AI API Keys| AIService
    ConfigService -->|Tool API Keys| MCPService
```

## 6. Electron IPC Data Flow

```mermaid
flowchart TD
    %% Main to Renderer Communication
    MainProcess[Main Process] -->|Create Window| BrowserWindow[Browser Window]
    BrowserWindow -->|Load| RendererProcess[Renderer Process]
    
    MainProcess -->|IPC Send| IPCMain[IPC Main]
    IPCMain -->|Message| IPCRenderer[IPC Renderer]
    IPCRenderer -->|Event| ReactApp[React App]
    
    %% Renderer to Main Communication
    ReactApp -->|invoke| IPCRenderer
    IPCRenderer -->|Request| IPCMain
    IPCMain -->|Handler| MainProcess
    MainProcess -->|Response| IPCMain
    IPCMain -->|Result| IPCRenderer
    IPCRenderer -->|Promise Resolution| ReactApp
    
    %% File System Access via IPC
    ReactApp -->|loadConfig Request| IPCRenderer
    IPCRenderer -->|invoke 'load-config'| IPCMain
    IPCMain -->|Handler| FileAccess[File System Access]
    FileAccess -->|File Content| IPCMain
    IPCMain -->|Result| IPCRenderer
    IPCRenderer -->|Config Data| ReactApp
```

## 7. Artifact Window Security Flow

```mermaid
flowchart TD
    %% HTML Content Generation
    AIService[AI Service] -->|Tool Result with HTML| HTMLContent[HTML Content]
    
    %% Content Security Processing
    HTMLContent -->|Parse| HTMLParser[HTML Parser]
    HTMLParser -->|Extract| UnsafeContent[Unsafe Content]
    UnsafeContent -->|Sanitize| ContentSanitizer[Content Sanitizer]
    ContentSanitizer -->|Apply CSP| CSPRules[Content Security Policy]
    CSPRules -->|Secure HTML| SandboxedContent[Sandboxed Content]
    
    %% Rendering in Artifact Window
    SandboxedContent -->|Set srcDoc| IframeSandbox[iframe with sandbox]
    IframeSandbox -->|Render| ArtifactWindow[Artifact Window]
    
    %% Security Boundaries
    ArtifactWindow -->|No Script Access| MainAppContext[Main App Context]
    ArtifactWindow -->|Restricted Network| RestrictedNetwork[Restricted Network Access]
    ArtifactWindow -->|No Storage Access| IsolatedStorage[Isolated Storage]
```

## 8. System State Transitions

```mermaid
stateDiagram-v2
    [*] --> ApplicationStartup
    
    ApplicationStartup --> ConfigurationLoading: Load Configs
    ConfigurationLoading --> AIInitialization: AI Config Loaded
    ConfigurationLoading --> MCPInitialization: MCP Config Loaded
    
    AIInitialization --> ReadyState: AI Service Ready
    MCPInitialization --> ToolRegistration: Register MCP Tools
    ToolRegistration --> ReadyState: Tools Ready
    
    ReadyState --> UserInputState: Awaiting User Input
    UserInputState --> ProcessingState: User Sends Message
    ProcessingState --> AIResponseState: Processing AI Response
    AIResponseState --> ToolCallState: AI Requests Tool
    AIResponseState --> DisplayResponseState: AI Response Ready (No Tool)
    
    ToolCallState --> ToolExecutionState: Execute Tool
    ToolExecutionState --> AIResponseState: Tool Result to AI
    
    DisplayResponseState --> UserInputState: Show Response
    
    UserInputState --> HistoryLoadingState: User Selects History
    HistoryLoadingState --> DisplayResponseState: Load Chat History
    
    UserInputState --> [*]: Close Application
```

## 9. Error Handling Flow

```mermaid
flowchart TD
    %% Error Sources
    AIError[AI API Error] -->|Catch| AIService[AI Service]
    ToolError[MCP Tool Error] -->|Catch| MCPService[MCP Service]
    ConfigError[Config Loading Error] -->|Catch| ConfigService[Config Service]
    StorageError[Storage Error] -->|Catch| ChatHistoryService[Chat History Service]
    
    %% Error Handling
    AIService -->|Format Error| ErrorState[Error State]
    MCPService -->|Format Error| ErrorState
    ConfigService -->|Format Error| ErrorState
    ChatHistoryService -->|Format Error| ErrorState
    
    %% Error Display
    ErrorState -->|UI Update| ErrorDisplay[Error Display Component]
    ErrorDisplay -->|Show to| User([User])
    
    %% Error Recovery
    ErrorDisplay -->|Retry Action| RetryFlow[Retry Flow]
    ErrorDisplay -->|Fallback Action| FallbackFlow[Fallback Flow]
    
    %% Error Logging
    ErrorState -->|Log Error| ErrorLog[Error Logging Service]
    ErrorLog -->|Persist| LocalStorage[(Local Storage)]
```

## 10. End-to-End Data Flow for a Complete User Interaction

```mermaid
sequenceDiagram
    participant User as User
    participant UI as UI Components
    participant AIService as AI Service
    participant VercelAI as Vercel AI-SDK
    participant AIModel as AI Model API
    participant MCPService as MCP Service
    participant StripeTool as Stripe Tool
    participant StripeAPI as Stripe API
    participant Storage as Local Storage
    
    User->>UI: Enter message "Create a payment link for $10"
    UI->>AIService: sendMessage(message)
    AIService->>VercelAI: useChat.append(message)
    VercelAI->>AIModel: API Request with message
    
    AIModel->>VercelAI: Response with tool call
    VercelAI->>AIService: Tool call event
    AIService->>MCPService: executeToolCall("stripe", "createPaymentLink", {amount: 10})
    MCPService->>StripeTool: createPaymentLink({amount: 10})
    
    StripeTool->>StripeAPI: API Request
    StripeAPI->>StripeTool: Payment Link Response
    
    StripeTool->>MCPService: Payment Link Result
    MCPService->>AIService: Tool Result
    AIService->>VercelAI: Submit Tool Result
    
    VercelAI->>AIModel: Continue with Tool Result
    AIModel->>VercelAI: Final Response with HTML
    VercelAI->>AIService: Response with HTML
    
    AIService->>UI: Update Chat Window
    AIService->>UI: Update Artifact Window with HTML
    
    AIService->>Storage: Save chat history
    
    UI->>User: Display AI response
    UI->>User: Display payment link in Artifact Window
    User->>UI: Click payment link
    UI->>User: Open payment page in browser
```

## 11. Data Storage Mapping

```mermaid
erDiagram
    CHAT_SESSION {
        string id PK
        string title
        timestamp created
        timestamp updated
    }
    
    MESSAGE {
        string id PK
        string sessionId FK
        string role
        string content
        timestamp created
        integer order
    }
    
    TOOL_CALL {
        string id PK
        string messageId FK
        string name
        json arguments
        timestamp created
    }
    
    TOOL_RESULT {
        string id PK
        string toolCallId FK
        json result
        string error
        timestamp created
    }
    
    API_KEY {
        string id PK
        string service
        string key
        timestamp created
    }
    
    CONFIG {
        string id PK
        string type
        json content
        timestamp updated
    }
    
    CHAT_SESSION ||--o{ MESSAGE : contains
    MESSAGE ||--o{ TOOL_CALL : has
    TOOL_CALL ||--o| TOOL_RESULT : produces
```

## 12. Secure Data Handling

```mermaid
flowchart TD
    %% Data Classification
    subgraph SensitiveData [Sensitive Data]
        direction TB
        AIApiKeys[AI API Keys]
        StripeApiKeys[Stripe API Keys]
        UserData[User Personal Data]
    end
    
    subgraph StandardData [Standard Data]
        direction TB
        ChatMessages[Chat Messages]
        ToolDefinitions[Tool Definitions]
        AppConfig[App Configuration]
    end
    
    %% Storage Mechanisms
    subgraph SecureStorage [Secure Storage]
        direction TB
        OSKeychain[OS Keychain]
        EnvVars[Environment Variables]
        EncryptedStore[Encrypted Local Store]
    end
    
    subgraph StandardStorage [Standard Storage]
        direction TB
        LocalStorage[Local Storage]
        ConfigFiles[Configuration Files]
    end
    
    %% Data Flow with Security Controls
    AIApiKeys -->|Secure Storage| OSKeychain
    StripeApiKeys -->|Secure Storage| OSKeychain
    UserData -->|Encrypted| EncryptedStore
    
    ChatMessages -->|Standard Storage| LocalStorage
    ToolDefinitions -->|Standard Storage| ConfigFiles
    AppConfig -->|Standard Storage| ConfigFiles
    
    %% Access Controls
    subgraph AccessControls [Access Controls]
        direction TB
        MainProcessOnly[Main Process Only]
        RendererProcessRestricted[Renderer Process Restricted]
    end
    
    SecureStorage -->|Limited Access| MainProcessOnly
    MainProcessOnly -->|Controlled IPC| RendererProcessRestricted
    StandardStorage -->|Direct Access| RendererProcessRestricted
    
    %% Security Boundaries
    classDef sensitive fill:#f9a,stroke:#a86
    classDef standard fill:#adf,stroke:#8ab
    classDef secure fill:#bfb,stroke:#8a8
    classDef standard_store fill:#ddf,stroke:#aac
    classDef access fill:#ffd,stroke:#cc8
    
    class SensitiveData sensitive
    class StandardData standard
    class SecureStorage secure
    class StandardStorage standard_store
    class AccessControls access