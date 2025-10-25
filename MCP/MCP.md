## MCP Overview 

MCP (Model Connection Protocol) is a standardized protocol designed to connect AI models (like LLMs) with external capabilities such as tools, data sources, and service,similar to how USB-C connects various devices through a single port.

### Key Benefits:

- Consistency: Developers implement MCP once and gain access to multiple tools.

- Plug-and-Play: Tools or services only need to implement MCP once to work across many AI applications.

- Ecosystem Growth: Reduced fragmentation, better interoperability, faster innovation.

### Analogy:
Just as USB-C simplifies device compatibility, MCP simplifies AI integration by acting as a universal adapter for AI applications and external capabilities.

### The M×N Integration Problem:

#### Without MCP (M×N Custom Integrations)
- Scenario: we have M AI applications and N external tools.

- Challenge: we need to write M × N custom connections.

- Result: Complex, costly, fragile, and hard to maintain

**Example:**

- 3 AI models (Claude, GPT, Gemini)

- 4 tools (Weather API, Calendar, Search, File Storage)

-Required integrations = 3 × 4 = 12

#### With MCP (M+N Standardized Integrations)
Scenario: Each AI application (M) implements the client side once.

Each tool/data source (N) implements the server side once.

Total integration effort: M + N

-  Example (same as above):

-  3 AI models + 4 tools = 7 total implementations

-  Saved effort = 12 – 7 = 5 fewer integrations

#### MCP Components

| Component  | Role                | Description                                                | Example                                 |
| ---------- | ------------------- | ---------------------------------------------------------- | --------------------------------------- |
| **Host**   | User-facing         | The AI app users interact with                             | Claude Desktop, LangChain app           |
| **Client** | Internal connector  | The part of the host that communicates with the MCP server | Handles MCP messages                    |
| **Server** | External capability | Offers tools, data, and prompts via MCP                    | A code execution backend or weather API |
 
#### MCP Capabilities
MCP lets AI apps use external capabilities in a structured way. Four main types:

| Capability   | Description            | Example                                    |
| ------------ | ---------------------- | ------------------------------------------ |
| **Tool**     | Executable function    | `getWeather(location)`, `runCode(code)`    |
| **Resource** | Read-only info         | Scientific paper database, product catalog |
| **Prompt**   | Predefined instruction | “Summarize this document”                  |
| **Sampling** | LLM review loop        | AI revisits its own answer and refines it  |

#### Collective Usage Example – Code Agent

| Entity   | Name             | Purpose                                  |
| -------- | ---------------- | ---------------------------------------- |
| Tool     | Code Interpreter | Executes LLM-generated code              |
| Resource | Documentation    | Provides API docs                        |
| Prompt   | Code Style       | Guides the tone/format of generated code |
| Sampling | Code Review      | LLM inspects & improves its own code     |

## MCP Architecture

The Model Connection Protocol (MCP) uses a modular client-server architecture that connects AI applications (models) with external capabilities (tools, data, services). This architecture is designed to:

- Enable interoperability between any AI app and any external service.

- Standardize how these components communicate.

- Support plug-and-play integration of new capabilities with minimal overhead.

### Key Architectural Components

| Component  | Role                       | Description                                                       |
| ---------- | -------------------------- | ----------------------------------------------------------------- |
| **Host**   | User-facing AI application | Orchestrates interactions between users, LLMs, and external tools |
| **Client** | Protocol manager           | Connects a Host to a specific MCP Server                          |
| **Server** | Capability provider        | Exposes tools, resources, prompts via MCP                         |

1. Host
   
The Host is the central AI interface that the user interacts with. It handles:
- User interaction (UI, prompts, chat)
- LLM orchestration
- Decision-making on capability invocation
- Rendering results back to the user

**Host Responsibilities:**
- Handle input/output with users
- Route appropriate tasks to MCP Clients
- Orchestrate requests/responses across multiple capabilities
- Maintain permissions and context
- Decide when to use LLM vs. when to call tools

2. Client

The Client lives inside the Host. It is responsible for all MCP communication with an external Server. 

- Each Client has a 1:1 relationship with a Server
- Manages connection, authentication, messaging
- Abstracts protocol-level logic (MCP handshake, capability discovery, response format)

**Responsibilities:**
- Connect to a Server using MCP
- Request capabilities (Tool, Resource, Prompt)
- Format and send invocation requests
- Relay responses back to the Host

3. Server

The Server is an external tool or service that provides specific functionalities to the AI system via MCP.

**Server Characteristics:**

Can be local (runs on same machine) or remote (network service)

- Provides one or more capabilities:

    1. Tools (e.g., math solver, weather API)

    2. Resources (e.g., document corpus, vector DB)

    3. Prompts (e.g., code templates)

    4. Sampling (self-refinement)

**Server Responsibilities:**
- Define capabilities in MCP-compliant format
- Receive and process requests from Clients
- Return standardized responses
- Optionally maintain logs, tokens, or usage stats

### Visual Representation:

User ↔ Host ↔ Client ↔ Server

## MCP Communication Protocol

MCP defines a standard way for AI Clients and Servers to communicate. It uses JSON-RPC 2.0 for structured, language-agnostic messaging and supports two transport mechanisms: stdio and HTTP + SSE.

### Message Types (JSON-RPC 2.0)
- Request
- Sent from Client → Server
- Starts an operation
- Contains id, method, and params

### Example:

{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "weather",
    "arguments": { "location": "San Francisco" }
  }
}

## MCP Capabilities 

The Model Connection Protocol (MCP) enables structured and secure interaction between AI clients (e.g., LLMs) and AI servers. It exposes four core capabilities (primitives) that allow the LLM to read, act, reason, and adapt within a safe boundary of control.

1. Tools
Tools are active functions or operations that can be executed by the LLM to perform side-effect-driven tasks (e.g., making API calls, sending messages, writing files).
2. Resources
Resources are read-only data sources (similar to REST GET calls) used to retrieve context or information.
3. Prompts
Prompts are predefined conversation templates or workflows that structure the AI interaction for specific tasks.
4. Sampling
Sampling is a server-initiated request for the client to invoke an LLM with a set of messages and return the result. Enables multi-step reasoning or recursive workflows.

## Understanding the MCP SDK
The Model Connection Protocol (MCP) defines how AI agents (e.g., LLMs) communicate with external systems in a secure and structured way. MCP SDKs simplify this by offering pre-built tools and abstractions for developers, allowing you to create MCP servers or clients without handling low-level networking or serialization.

### What MCP SDKs Do

MCP SDKs (Software Development Kits) for languages like Python, JavaScript, and others help with:

| Feature                            | Description                                                  |
| ---------------------------------- | ------------------------------------------------------------ |
| Protocol Communication          | Handles the JSON message passing between clients and servers |
| Capability Registration         | Lets you register `Tools`, `Resources`, and `Prompts`        |
| Serialization / Deserialization | Translates messages into/from Python objects                 |
| Connection Management           | Opens and maintains communication with clients               |
| Error Handling                  | Gracefully catches and manages errors within the MCP flow    |

### MCP Clients
MCP Clients are essential components in the Model Connection Protocol (MCP) ecosystem. They serve as the bridge between an AI Host (e.g., Claude Desktop, VS Code, or Cursor) and MCP Servers, enabling external tools or services to be used within the AI environment.

#### What Are MCP Clients?

An MCP Client is a module within an AI application (the Host) that manages communication with MCP Servers, which expose external tools (e.An MCP Client is a module within an AI application (the Host) that manages communication with MCP Servers, which expose external tools (e.g., file systems, APIs, browsers).g., file systems, APIs, browsers).Think of the AI application (Host) as your smartphone, the MCP Client as the app launcher, and the MCP Servers as the apps (tools) themselves.

## Gradio MCP Integration

Gradio’s integration with MCP provides an accessible entry point to the MCP ecosystem. By leveraging Gradio’s simplicity and adding MCP’s standardization, developers can quickly create both human-friendly interfaces and AI-accessible tools with minimal code.



