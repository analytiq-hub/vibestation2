Okay, I will transform the "Project Start Prompt: Vibestation - AI+MCP Desktop Client (with Stripe & Unit Testing)" (which we're treating as the PRD) into a detailed, phased project plan with micro-tasks, ensuring every micro-task and phase has an AI-Verifiable End Result.

# Project Plan: Vibestation - AI+MCP Desktop Client

## Overall Project Goal (Derived from PRD):
To develop and deliver an initial Minimum Viable Product (MVP) of Vibestation, a locally-run AI+MCP desktop client. This MVP will successfully demonstrate core functionalities including:
1.  A desktop application shell built with Electron and React.
2.  AI chat interaction via Vercel AI-SDK with Anthropic and OpenAI models.
3.  Management and invocation of MCP tools using `mcp-client`, exemplified by a Stripe MCP tool for basic payment operations.
4.  Local chat history persistence.
5.  All core functionalities are to be unit-tested using Jest or Vitest, achieving high code coverage for new TypeScript/JavaScript code.
Successful completion is defined by all phases' AI-Verifiable End Goals being met, all P0/P1 unit tests passing, and core features functioning as described in the PRD sections regarding "Key Objectives for Initial Version (MVP)."

---

## Phase 1: Project Setup, Basic Electron Shell & Core Dependencies
**Phase AI-Verifiable End Goal:** The project repository is initialized with the specified technology stack. A basic Electron application window launches successfully displaying a placeholder for the main chat interface. Core dependencies (Electron, React, TypeScript, Tailwind CSS, chosen testing framework) are installed and configured. Initial unit tests for placeholder components pass. A CI pipeline executes basic linting and test jobs successfully.
**Relevant PRD Sections/Requirements:** Technology Stack, Key Objectives for Initial Version (MVP) - Electron Application Shell, Unit Testing Strategy.

### Micro-task 1.1: Initialize Project Repository and Basic Structure
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   A Git repository is created and initialized (e.g., on GitHub, GitLab).
    *   A `package.json` file exists and lists `electron`, `react`, `react-dom`, `typescript`, `tailwindcss`, and the chosen testing framework (e.g., `jest` or `vitest`) as dependencies.
    *   `tsconfig.json` is present and configured for a React/TypeScript project.
    *   `tailwind.config.js` (or equivalent) is present.
    *   Basic project directory structure (e.g., `src/`, `public/`, `tests/`) is created.
    *   Script `npm install` (or `yarn install`) completes without errors.
*   **Relevant PRD Sections/Requirements:** Technology Stack.

### Micro-task 1.2: Configure Basic Electron Application Shell
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   An `electron/main.ts` (or `.js`) file exists.
    *   Running an npm script (e.g., `npm start`) launches an Electron main window.
    *   The launched window's title is "Vibestation".
    *   A placeholder React component (e.g., `App.tsx` in `src/`) is rendered within the Electron window, displaying static text like "Vibestation MVP Loading...".
*   **Relevant PRD Sections/Requirements:** Technology Stack - Electron, React; Key Objectives - Electron Application Shell.

### Micro-task 1.3: Setup Testing Framework and Initial Placeholder Tests
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   Configuration files for the chosen testing framework (e.g., `jest.config.js` or `vitest.config.ts`) exist.
    *   An npm script (e.g., `npm test`) successfully executes the test runner.
    *   At least one placeholder unit test file (e.g., `src/App.test.tsx`) exists.
    *   The placeholder unit test, which tests the basic rendering of the `App.tsx` placeholder component (e.g., checks for the presence of "Vibestation MVP Loading..." text), passes when `npm test` is run.
*   **Relevant PRD Sections/Requirements:** Unit Testing Strategy, Technology Stack - Testing Framework.

### Micro-task 1.4: Implement Basic CI Pipeline
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   A CI configuration file (e.g., `.github/workflows/main.yml` or `.gitlab-ci.yml`) exists in the repository.
    *   The CI pipeline includes jobs for:
        *   Installing dependencies (`npm install`).
        *   Linting the codebase (e.g., using ESLint with TypeScript plugins - configuration to be added in a later micro-task, for now, the job can be a placeholder).
        *   Running unit tests (`npm test`).
    *   Upon a commit/push to the main branch (or feature branches), the CI pipeline is triggered and all defined jobs (install, placeholder lint, test) complete successfully without manual intervention.
*   **Relevant PRD Sections/Requirements:** Unit Testing Strategy (Implied by modern development practices for tested applications).

---

## Phase 2: AI Model Connectivity & Basic Chat Interface
**Phase AI-Verifiable End Goal:** The Vibestation application can connect to configured AI models (Anthropic & OpenAI via Vercel AI-SDK). A basic chat UI allows users to send messages and receive responses from the selected AI model. Configuration for AI models is loaded from `ai.config.json`. All new UI components and logic are unit-tested and pass.
**Relevant PRD Sections/Requirements:** Technology Stack - Vercel AI-SDK; Key Objectives - AI Model Connectivity, AI Chat Loop; Unit Testing Strategy.

### Micro-task 2.1: Integrate Vercel AI-SDK and Configure Model Providers
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   `@ai-sdk/react` (or core Vercel AI SDK package for non-React usage if applicable to backend processes, though PRD implies UI usage) and `ai` (Vercel AI SDK core) are listed as dependencies in `package.json` and installed.
    *   An `ai.config.json` file template exists, specifying placeholder structures for Anthropic and OpenAI provider configurations (e.g., API key placeholders, model names).
    *   A TypeScript module/service (e.g., `src/services/aiModelService.ts`) exists that can load and parse `ai.config.json`.
    *   Unit tests for `aiModelService.ts` verify:
        *   Successful parsing of a valid sample `ai.config.json`.
        *   Error handling for a malformed or missing `ai.config.json`.
*   **Relevant PRD Sections/Requirements:** Technology Stack - Vercel AI-SDK, Configuration (`ai.config.json`); Key Objectives - AI Model Connectivity.

### Micro-task 2.2: Implement Basic Chat UI with Vercel AI-SDK Hooks
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   A React component (e.g., `ChatWindow.tsx` in `src/components/`) is created.
    *   `ChatWindow.tsx` uses Vercel AI-SDK's `useChat` hook (or equivalent).
    *   The UI displays an input field for user messages and a submit button.
    *   The UI displays a list of messages (user and AI).
    *   Unit tests for `ChatWindow.tsx` (mocking `useChat` hook and AI service calls) verify:
        *   Correct rendering of input field and button.
        *   User input is correctly captured.
        *   Messages provided by the (mocked) `useChat` hook are rendered in the message list.
        *   The (mocked) `append` function from `useChat` is called when the user submits a message.
*   **Relevant PRD Sections/Requirements:** Key Objectives - AI Chat Loop (UI components); Technology Stack - React, Vercel AI-SDK.

### Micro-task 2.3: Implement AI Message Sending and Receiving Logic
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   The `ChatWindow.tsx` component, when a message is submitted:
        *   Calls a function in `aiModelService.ts` to interact with the Vercel AI-SDK using parameters from `ai.config.json` (e.g., selected model, API key placeholder).
        *   A unit test for this interaction (mocking the actual AI-SDK call and `ai.config.json` loading) verifies that the service function is called with expected parameters (user message, mocked model config).
        *   (Actual AI calls with real keys will be manually tested due to external dependency/cost, but unit tests will verify the flow up to the AI-SDK call).
    *   A (mocked) response from the AI (simulated via mocked AI-SDK service) is displayed in the `ChatWindow.tsx` messages list.
*   **Relevant PRD Sections/Requirements:** Key Objectives - AI Model Connectivity, AI Chat Loop.

---

## Phase 3: MCP Client Integration & Basic Tool Loading
**Phase AI-Verifiable End Goal:** Vibestation integrates the `mcp-client` library. MCP server configurations are loaded from `mcp.config.json`. The system can parse this configuration and make tool definitions available in a format compatible with Vercel AI-SDK. Unit tests for MCP configuration loading and tool definition formatting pass.
**Relevant PRD Sections/Requirements:** Technology Stack - `mcp-client`, Configuration (`mcp.config.json`); Key Objectives - MCP Server Management.

### Micro-task 3.1: Integrate `mcp-client` Library
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   `mcp-client` is listed as a dependency in `package.json` and installed.
    *   A TypeScript module/service (e.g., `src/services/mcpService.ts`) is created to encapsulate `mcp-client` interactions.
*   **Relevant PRD Sections/Requirements:** Technology Stack - `mcp-client`.


### Micro-task 3.2: Implement `mcp.config.json` Loading and Parsing
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   An `mcp.config.json` file template exists, specifying placeholder structures for MCP server configurations (e.g., local STDIO server paths, remote SSE URLs, tool metadata).
    *   `mcpService.ts` contains functions to load and parse `mcp.config.json`.
    *   Unit tests for these functions in `mcpService.ts` verify:
        *   Successful parsing of a valid sample `mcp.config.json`.
        *   Correct extraction of server configurations and tool metadata.
        *   Error handling for a malformed or missing `mcp.config.json`.
*   **Relevant PRD Sections/Requirements:** Configuration (`mcp.config.json`); Key Objectives - MCP Server Management.

### Micro-task 3.3: Format Tool Definitions for Vercel AI-SDK
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   `mcpService.ts` includes a function that transforms the tool metadata parsed from `mcp.config.json` into an array of tool definition objects compatible with the Vercel AI-SDK's `tools` option (typically an OpenAPI-like schema).
    *   Unit tests for this transformation function verify that, given sample tool metadata, the output is a correctly structured array of Vercel AI-SDK compatible tool definitions.
    *   A mechanism exists (e.g., a function in `aiModelService.ts` or integration in `ChatWindow.tsx`) to provide these formatted tool definitions to the Vercel AI-SDK `useChat` hook options. Unit tests for this mechanism confirm that the (mocked) `useChat` is initialized with the correctly formatted tools.
*   **Relevant PRD Sections/Requirements:** Key Objectives - MCP Server Management, AI Chat Loop (MCP Tool Calling Integration).

---

## Phase 4: Stripe MCP Tool Implementation & AI Tool Calling
**Phase AI-Verifiable End Goal:** A basic Stripe MCP tool is implemented using the Stripe Node.js SDK, capable of performing specified read-only/safe actions (listing payments, checking balance, creating payment links). This tool can be invoked by the AI via the `mcp-client` and Vercel AI-SDK tool-calling mechanism. Secure (placeholder for actual keys) handling of Stripe API keys is part of the tool's configuration. All new logic is unit-tested and passes.
**Relevant PRD Sections/Requirements:** Technology Stack - Stripe SDK; Key Objectives - Stripe Integration (as an example MCP Tool), AI Chat Loop (MCP Tool Calling Integration); Unit Testing Strategy - Stripe MCP Tool.

### Micro-task 4.1: Setup Stripe SDK and Basic Stripe MCP Tool Structure
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   `stripe` (Node.js SDK) is listed as a dependency in `package.json` and installed.
    *   A new MCP tool module (e.g., `src/mcp_tools/stripeTool.ts`) is created.
    *   This module includes placeholder functions for: `listRecentPayments`, `checkBalance`, `createPaymentLink`.
    *   `mcp.config.json` includes a configuration entry for this Stripe tool, specifying its callable actions and a placeholder for API key retrieval/configuration.
    *   A placeholder secure API key retrieval mechanism (e.g., attempts to read from an environment variable `STRIPE_API_KEY_PLACEHOLDER`) is implemented within `stripeTool.ts`.
*   **Relevant PRD Sections/Requirements:** Technology Stack - Stripe SDK; Key Objectives - Stripe Integration.

### Micro-task 4.2: Implement Stripe MCP Tool Actions
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   The functions in `stripeTool.ts` (`listRecentPayments`, `checkBalance`, `createPaymentLink`) are implemented to call the corresponding Stripe SDK methods.
    *   Unit tests for `stripeTool.ts` (mocking the Stripe SDK, e.g., `stripe.paymentIntents.list`, `stripe.balance.retrieve`, `stripe.paymentLinks.create`) verify:
        *   Stripe SDK methods are called with expected parameters.
        *   (Mocked) responses from the Stripe SDK are correctly processed and returned by the tool functions.
        *   The API key placeholder mechanism is called.
        *   Error handling from mocked Stripe SDK calls is implemented.
*   **Relevant PRD Sections/Requirements:** Key Objectives - Stripe Integration; Unit Testing Strategy - Stripe MCP Tool.

### Micro-task 4.3: Enable AI Invocation of the Stripe MCP Tool
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   When the AI model (via Vercel AI-SDK) generates a tool call request for one of the defined Stripe tool actions:
        *   `mcpService.ts` (or a related service) correctly identifies and dispatches this call to the `stripeTool.ts` (via a mocked `mcp-client.callTool` if `mcp-client` is abstracting the tool execution).
        *   Unit tests for `mcpService.ts` verify this dispatching logic (mocking the tool call request from AI-SDK and the execution of the Stripe tool function).
    *   The (mocked) result from `stripeTool.ts` is correctly formatted and returned to the Vercel AI-SDK to be passed back to the AI model.
    *   Unit tests verify this result formatting and return flow.
*   **Relevant PRD Sections/Requirements:** Key Objectives - AI Chat Loop (MCP Tool Calling Integration).

---

## Phase 5: Advanced Chat Features & UI Refinements
**Phase AI-Verifiable End Goal:** Vibestation implements local chat history (saving and loading) and the optional left/right UI columns (for chat history selection and artifact display). Core functionality remains stable and unit tests pass.
**Relevant PRD Sections/Requirements:** Key Objectives - Electron Application Shell (sidebars), Local Chat History, Right Artifact Window.

### Micro-task 5.1: Implement Local Chat History Service
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   A service module (e.g., `src/services/chatHistoryService.ts`) is created.
    *   Functions exist to:
        *   `saveChatSession(sessionId, messages)`: Persists messages to local storage (e.g., using `localStorage` or Electron's store, or simple file I/O for MVP).
        *   `loadChatSession(sessionId)`: Retrieves messages from local storage.
        *   `listChatSessions()`: Returns a list of saved session IDs/metadata.
    *   Unit tests for `chatHistoryService.ts` (mocking the chosen local storage mechanism) verify:
        *   Saving and loading of chat messages are correct.
        *   Listing sessions functions as expected.
        *   Error handling for storage operations is implemented.
*   **Relevant PRD Sections/Requirements:** Key Objectives - Local Chat History; Unit Testing Strategy - Chat History Service.

### Micro-task 5.2: Integrate Chat History into UI (Left Column)
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   A React component for the left sidebar (e.g., `ChatHistorySidebar.tsx`) exists.
    *   The sidebar calls `chatHistoryService.listChatSessions()` and displays the list of (mocked for tests) sessions.
    *   Selecting a session in the sidebar calls `chatHistoryService.loadChatSession()` and updates the main `ChatWindow.tsx` with the loaded messages (via mocked service and state updates).
    *   A "New Chat" button exists and clears the current chat session in `ChatWindow.tsx`.
    *   Unit tests for `ChatHistorySidebar.tsx` and its integration with `App.tsx`/`ChatWindow.tsx` (mocking `chatHistoryService`) verify these interactions and UI updates.
*   **Relevant PRD Sections/Requirements:** Key Objectives - Electron Application Shell (optional left column), Local Chat History.

### Micro-task 5.3: Implement Artifact Window (Right Column) for HTML Display
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   A React component for the right sidebar/artifact window (e.g., `ArtifactWindow.tsx`) exists.
    *   The component can receive HTML content (e.g., as a prop).
    *   It renders the provided HTML content within a sandboxed `iframe` (or Electron's `webview` with `sandbox` attribute) to prevent security issues.
    *   A mechanism exists (e.g., via state management or direct prop passing in `App.tsx`) to pass HTML content from an AI tool response (e.g., a payment link from the Stripe tool) to `ArtifactWindow.tsx`.
    *   Unit tests for `ArtifactWindow.tsx` verify it correctly sets the `srcDoc` (or equivalent) of the iframe/webview with provided HTML.
    *   Unit tests verify the sandboxing attributes are set on the iframe/webview.
*   **Relevant PRD Sections/Requirements:** Key Objectives - Electron Application Shell (optional right artifact window), Right Artifact Window.

---

## Phase 6: Comprehensive Testing, Refinement & Finalization
**Phase AI-Verifiable End Goal:** All critical path unit tests pass with high code coverage (as defined by project standards, e.g., >80% for new code). Configuration files are finalized. Known P0/P1 bugs (from automated tests) are fixed. The application builds successfully for target platforms (initial setup for one platform, e.g., current OS).
**Relevant PRD Sections/Requirements:** Unit Testing Strategy; Implied finalization steps.

### Micro-task 6.1: Review and Augment Unit Test Coverage
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   A code coverage report is generated by the test runner (e.g., `npm test -- --coverage`).
    *   The coverage report indicates that all new modules and critical functions developed in Phases 1-5 meet the project's defined coverage target (e.g., >80% statement coverage).
    *   Any identified gaps in unit test coverage for critical paths are addressed by adding new tests, and these tests pass.
*   **Relevant PRD Sections/Requirements:** Unit Testing Strategy.

### Micro-task 6.2: Finalize Configuration Schemas and Default Values
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   `ai.config.json.template` and `mcp.config.json.template` (or equivalent sample/documentation files) accurately reflect all configurable options used by the application.
    *   These template files include clear comments or documentation explaining each configuration option.
    *   The application robustly handles scenarios where optional configuration values are missing by using sensible defaults, and this default behavior is verified by unit tests.
*   **Relevant PRD Sections/Requirements:** Configuration (`ai.config.json`, `mcp.config.json`).

### Micro-task 6.3: Address High-Priority Bugs from Automated Testing
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   All failing unit tests identified as P0 or P1 severity are fixed.
    *   Running `npm test` results in all tests passing.
*   **Relevant PRD Sections/Requirements:** Unit Testing Strategy.

### Micro-task 6.4: Implement Basic Application Build Process
*   **AI-Verifiable Deliverable/Completion Criteria:**
    *   NPM scripts (e.g., `npm run build:electron`) are configured using tools like `electron-builder` or `electron-packager`.
    *   Executing the build script successfully generates a distributable application package for at least one target platform (e.g., a `.dmg` for macOS, `.exe` for Windows, or an `AppImage` for Linux, as per initial developer OS) without errors.
    *   (Note: Actual cross-platform build testing is more extensive, this is an initial setup.)
*   **Relevant PRD Sections/Requirements:** Technology Stack - Electron (implies distributable).

---

## Final Validation Approach (Post All Phases):
Successful completion of the overall project goal will be validated by:
1.  **All Phase AI-Verifiable End Goals Met:** Confirmation that all AI-Verifiable End Goals for Phases 1 through 6 have been achieved as per their definitions.
2.  **Successful Execution of All Unit Tests:** The command `npm test` executes and all unit tests pass, with code coverage reports meeting the defined project standard.
3.  **Core Feature Manual Smoke Test (checklist based on PRD MVP Objectives):**
    *   Application launches successfully (`Micro-task 1.2`).
    *   User can type a message and receive a (mocked or live if configured) response from a configured AI model (e.g., Anthropic Claude Sonnet 3.7) (`Micro-task 2.2, 2.3`).
    *   The Stripe MCP tool can be (conceptually, through AI prompting or a developer debug interface) invoked, and if prompted for an action like "create a payment link", the (mocked or live) tool result leads to an HTML link being displayed in the artifact window (`Micro-task 4.2, 4.3, 5.3`).
    *   Chat history can be saved and reloaded across application restarts (`Micro-task 5.1, 5.2`).
4.  **Application Package Generation:** A basic application build successfully generates a runnable package for the primary development OS (`Micro-task 6.4`).
