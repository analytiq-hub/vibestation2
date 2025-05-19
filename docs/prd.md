---

**Project Start Prompt: Vibestation - AI+MCP Desktop Client (with Stripe & Unit Testing)**

**1. Project Vision & Goal:**
We are building **Vibestation**, an AI and MCP (Model-Controller-Presenter, referring to AI tool-calling capabilities) client designed to run locally on a user's computer. The initial version will be a general-purpose client similar to Claude Desktop, allowing users to chat with AI models, load MCP servers, and have the AI call MCP tools.
The long-term vision is to extend Vibestation into a highly customizable platform that can be tailored for specific human roles (e.g., "Vibe Trader" for crypto traders, programmers, lawyers) by intelligently managing context, prompts, and available tools for the AI. This includes enabling interactions with third-party services like Stripe.

**Primary Source Document:** "Product Requirements" by Andy Singleton.

**2. Core Problem to Solve (Initial):**
Provide a user-friendly desktop interface for interacting with AI models that can utilize external tools (MCPs) to perform tasks, including interacting with services like Stripe.

**3. Key Technical Challenge (Overall):**
Effectively controlling the context provided to the AI, ensuring it has a relevant and limited set of tools and prompts for the current task to avoid confusion and enhance performance. Securely managing API keys and sensitive data for third-party integrations.

**4. Technology Stack (Mandatory):**
*   **Language:** JavaScript & TypeScript
*   **Core Framework:** Electron (for desktop application, installer, UI framework)
*   **UI Library:** React
*   **UI Component Framework:** Chakra UI (for accessible, themeable component library)
*   **Styling:** Tailwind CSS (complementary to Chakra UI for custom styling needs)
*   **AI Model Interaction:** Vercel AI-SDK (for connecting to AI models, chat interface, parsing/running MCP tool calls)
*   **MCP Client Library:** `mcp-client` (npm package for connecting to/managing MCP servers and tools)
*   **Payment Integration (as an MCP tool capability):** Stripe SDK (Node.js version, `stripe`)
*   **Testing Framework:** Jest or Vitest (developer choice, ensure good TypeScript support)
*   **Configuration:**
    *   `ai.config.json` (or similar) for AI-SDK provider parameters (Anthropic, OpenAI, etc.)
    *   `mcp.config.json` (or similar) for MCP server configurations (local STDIO, remote SSE), including potential configuration for a Stripe MCP tool.
    *   Secure storage/method for user-provided API keys (e.g., OS keychain, environment variables, or an encrypted local config, TBD).

**5. Key Objectives for Initial Version (MVP):**

*   **Electron Application Shell:**
    *   Basic window structure resembling Claude Desktop (Main chat, optional left chat history, optional right artifact window).
*   **AI Model Connectivity (via Vercel AI-SDK):**
    *   Connect to Anthropic Claude models (target Claude Sonnet 3.7 for initial tool-calling tests).
    *   Support for OpenAI models.
    *   Configuration driven by `ai.config.json`.
*   **AI Chat Loop (via Vercel AI-SDK UI components):**
    *   User inputs text, AI responds.
    *   **MCP Tool Calling Integration:** AI SDK detects tool calls, Vibestation uses `mcp-client` to execute tools, results returned to AI.
*   **MCP Server Management (via `mcp-client`):**
    *   Load MCP servers based on `mcp.config.json`.
    *   Support local STDIO and remote SSE servers.
    *   Tools loaded into an AI-SDK compatible data structure.
*   **Stripe Integration (as an example MCP Tool):**
    *   Develop an MCP tool that wraps the Stripe Node.js SDK.
    *   The tool should allow AI-driven actions like:
        *   Listing recent payments.
        *   Checking balance.
        *   Creating a payment link (returning the URL).
        *   (Other simple, read-only or safe write operations).
    *   Requires a secure way for users to provide their Stripe API keys to this MCP tool (not hardcoded). This configuration should be part of the MCP tool's setup within `mcp.config.json` or a related secure store.
*   **Local Chat History:**
    *   Save and retrieve chat sessions locally.
*   **Right Artifact Window:**
    *   Display HTML output from AI (e.g., a Stripe payment link displayed as a clickable button/link).

**6. Licensing:**
*   Utilize components with open-source licenses (MIT, Apache preferred). Stripe SDK has its own license to adhere to.

**7. Unit Testing Strategy:**
*   **Goal:** Achieve high unit test coverage for all new TypeScript/JavaScript code.
*   **Framework:** Jest or Vitest.
*   **General Principles:**
    *   Test individual functions, methods, and React components in isolation.
    *   Mock all external dependencies:
        *   Vercel AI-SDK calls.
        *   `mcp-client` calls.
        *   Stripe SDK calls.
        *   Electron APIs (main/renderer communication, dialogs, file system if used directly).
        *   `localStorage` or other local storage mechanisms.
        *   Network requests.
    *   Focus on: inputs, outputs, state changes, and side effects.
    *   Cover: happy paths, edge cases (e.g., empty inputs, invalid data), and error handling.
*   **Specific Areas for Unit Tests (examples):**
    *   **Configuration Loading:**
        *   Test parsing of `ai.config.json` and `mcp.config.json`.
        *   Test validation of configuration structures.
        *   Test error handling for malformed or missing config files.
    *   **React Components:**
        *   Test rendering output given different props.
        *   Test event handlers (e.g., `onClick` on a "New Chat" button updates state correctly).
        *   Test conditional rendering logic.
    *   **AI Interaction Logic (wrappers around AI-SDK):**
        *   Test that messages are correctly formatted for the AI-SDK.
        *   Test that tool call requests from AI-SDK are correctly identified and parsed.
        *   Test that tool results are correctly formatted and sent back via AI-SDK.
        *   Mock AI-SDK's `useChat`, `generateText`, etc., to verify interactions.
    *   **MCP Client Logic (wrappers around `mcp-client`):**
        *   Test loading and starting of MCP servers (mock `mcp-client.startServer`).
        *   Test tool invocation (mock `mcp-client.callTool`).
        *   Test formatting of tool definitions for AI-SDK.
    *   **Stripe MCP Tool:**
        *   Mock the Stripe SDK (e.g., `stripe.paymentIntents.create`).
        *   Test that the MCP tool correctly calls Stripe SDK methods based on AI input.
        *   Test secure retrieval and usage of Stripe API keys (mock the secure storage mechanism).
        *   Test transformation of Stripe API responses into a format suitable for the AI.
    *   **Chat History Service:**
        *   Test saving a chat session (mock `localStorage.setItem`).
        *   Test loading a chat session (mock `localStorage.getItem`).
        *   Test listing chats.
    *   **Electron Main Process Logic (if any beyond boilerplate):**
        *   Test IPC message handlers (mock `ipcMain.on`, `event.reply`).
        *   Test window management functions.
    *   **Utility Functions:** Any standalone helper functions.

**8. Initial Work Plan Focus (incorporating Stripe & Testing):**
1.  Setup Electron, Node.js, React, TailwindCSS project. **Include setup for the chosen testing framework.**
2.  Implement basic window layout. **Write basic rendering tests for components.**
3.  Integrate Vercel AI-SDK: Configure for Anthropic. **Write unit tests for AI-SDK interaction logic (mocking SDK).**
4.  Implement basic AI chat functionality. **Test chat state management and message flow.**
5.  Integrate `mcp-client`: Add preload script to load MCP servers/tools. **Write unit tests for `mcp-client` interaction logic (mocking client).**
6.  Develop the Stripe MCP Tool. **Write thorough unit tests for the Stripe tool, mocking Stripe SDK and API key handling.**
7.  Enhance AI chat to support tool calls, including the Stripe tool. **Test the full tool-calling loop.**
8.  Implement chat history (local storage). **Test chat history service functions.**
9.  Implement the right artifact window for displaying HTML. **Test component rendering with HTML content.**

**9. Inspirations & Future Considerations (from Roo Code & Product Requirements):**
*   **"Modes" (Roo Code):** Future: Configure distinct "modes" with pre-set AI models, prompts, and specific MCP tools (e.g., a "Finance Vibe" that pre-loads the Stripe tool).
*   **SPARC Orchestrator / Planning (Roo Code):** Future: Structured planning for complex tasks.
*   **Context Management Techniques (Roo Code):** "Memory Bank," "Pheremone File," "Intelligent Context Condensation."
*   **Local Storage Expansion:** Logs, user-defined tools/agents/prompts. Consider `pglite`.
*   **Platform Monetization:** Future: Platform-level Stripe integration for Vibestation subscriptions or paid "Vibes" (distinct from the user-facing Stripe MCP tool).

**Action:**
Begin development focusing on the "Key Objectives for Initial Version (MVP)" using the specified "Technology Stack," with a strong emphasis on writing unit tests concurrently with feature development as outlined in the "Unit Testing Strategy."

---