Create CURSOR files for this project
We are going to create a Cursor rules file for the Jamey Code project. The Cursor rules file (usually named .cursorrules) is used to guide the Cursor AI when working on the project. It should include information about the project structure, coding conventions, and any specific instructions for the AI.

Given the project structure and the fact that it's a Rust workspace with multiple crates, we should include:

The overall architecture (as described in the previous steps)

The purpose of each crate

Coding conventions (like using anyhow for error handling, tracing for logging, etc.)

How to add new tools, providers, or orchestration modes

Let's create a comprehensive .cursorrules file.

I'll create comprehensive Cursor rules files for the Jamey Code project to guide AI assistance and maintain consistency across the codebase.

1. Main Cursor Rules
markdown:.cursorrules
# Jamey Code - Digital Twin Project Rules

## Project Architecture
- **Pure Core First**: `jamey-core` must have NO IO dependencies (no HTTP, no filesystem, no terminal)
- **Runtime Daemon**: All actual IO happens in `jamey-runtime` 
- **Thin Clients**: CLI/TUI/Desktop are just API clients to the runtime
- **Protocol-Driven**: Use `jamey-protocol` for all cross-boundary communication

## Code Organization
- **Workspace Structure**: Follow the established crate hierarchy
- **Dependency Direction**: 
  - `core` → `protocol` (allowed)
  - `runtime` → `core` + `providers` + `tools` (allowed)  
  - `clients` → `protocol` only (allowed)
  - NEVER: `core` → `runtime` or `core` → `providers`

## Rust Conventions
- **Error Handling**: Use `anyhow::Result` in binaries, `thiserror` in libraries
- **Async**: Use `#[async_trait]` for trait methods, `tokio` for runtime
- **Serialization**: All protocol types must derive `Serialize`/`Deserialize`
- **Logging**: Use `tracing` macros (`info!`, `debug!`, `error!`)

## Tool System Rules
- **Core Tools**: Define interface in `jamey-core::tool_protocol`
- **Implementation**: Actual tools live in `jamey-tools` 
- **External Tools**: Support both Rust traits and process/HTTP plugins
- **Security**: No guardrails by design - assume trusted environment

## API Design Rules
- **Runtime API**: Use Axum for HTTP, WebSocket for real-time events
- **Message Protocol**: JSON over HTTP/WebSocket following `jamey-protocol`
- **Session Management**: All state managed by runtime, clients are stateless
- **Streaming**: Use WebSocket for tool progress and message streaming

## Enterprise Integration Priority
1. **Microsoft 365**: Outlook, Teams, Graph API
2. **Security Tools**: Zscaler, Rapid7
3. **Windows Integration**: COM, notifications, system APIs
4. **Custom Business Tools**: Extensible plugin system

## Testing Strategy
- **Unit Tests**: Pure logic in `jamey-core` (no mocks needed)
- **Integration Tests**: Runtime API and tool execution
- **Client Tests**: API client implementations
- **No UI Testing**: Clients are thin, focus on protocol compliance

## When Adding New Features
1. Start with protocol definitions in `jamey-protocol`
2. Add pure domain logic in `jamey-core` 
3. Implement runtime support in `jamey-runtime`
4. Update clients to use new features
5. NEVER put IO in core, NEVER put business logic in clients

## Code Style Enforcement
- **Format**: `cargo fmt` before commit
- **Lint**: `cargo clippy -- -D warnings`
- **Types**: Prefer strong typing over stringly-typed data
- **Documentation**: All public APIs must have doc comments
2. Core-Specific Rules
markdown:jamey-core/.cursorrules
# Jamey Core - Pure Domain Logic Rules

## Absolute Bans
- NO `std::fs`, `std::net`, `tokio::fs`, `reqwest`, `axum`
- NO terminal operations (`crossterm`, `ratatui`)
- NO environment variables
- NO file I/O of any kind
- NO HTTP clients or servers

## Allowed Dependencies
- `serde`, `serde_json` (for data structures)
- `thiserror` (for error types)
- `async-trait` (for async trait methods)
- `uuid` (for identifiers)
- `chrono` (for timestamps)
- `jamey-protocol` (for shared types)

## Architecture Rules
- **Pure Functions**: All logic must be deterministic based on inputs
- **Session Management**: Handle conversation state and agent context
- **Orchestration Patterns**: Implement multi-agent workflows
- **Tool Protocol**: Define tool interfaces, don't implement them

## Example of CORRECT Code
```rust
// ✅ Pure domain logic
impl Orchestrator for SingleAgentMode {
    async fn process(&self, session: &Session, message: Message) -> Result<AgentResponse> {
        // Decision logic based only on session state and message
        if session.state.iteration_count > self.max_iterations {
            return Ok(AgentResponse::rate_limited());
        }
        // Pure business logic here
    }
}
Example of INCORRECT Code
rust
// ❌ IO in core - ABSOLUTELY FORBIDDEN
impl Orchestrator for SingleAgentMode {
    async fn process(&self, session: &Session, message: Message) -> Result<AgentResponse> {
        // ❌ Reading files
        let config = std::fs::read_to_string("config.json")?;
        // ❌ HTTP calls
        let response = reqwest::get("http://api.example.com").await?;
        // ❌ Environment variables  
        let key = std::env::var("API_KEY")?;
    }
}
Testing Strategy
Pure Unit Tests: No mocks needed, just test logic

Property Testing: Use proptest for complex logic

Integration: Test with simulated tool responses

When Implementing Agents
Define agent behavior in pure Rust

Use session state for context

Return tool calls as data, don't execute them

Handle tool results as pure data transformations

text

## 3. Runtime-Specific Rules

```markdown:jamey-runtime/.cursorrules
# Jamey Runtime - Daemon & IO Rules

## Responsibilities
- **HTTP API**: Axum server with REST + WebSocket endpoints
- **Tool Execution**: Run both Rust tools and external plugins
- **Session Persistence**: Manage conversation state storage
- **Provider Integration**: Handle actual LLM API calls
- **Client Management**: WebSocket connections for real-time updates

## Allowed Dependencies
- `axum`, `tokio`, `tower` (for HTTP server)
- `reqwest` (for outgoing HTTP)
- `tokio::fs`, `sqlx` (for persistence)
- `jamey-providers`, `jamey-tools` (for implementations)
- All `jamey-core` and `jamey-protocol` types

## Architecture Rules
- **API First**: All features must be exposed via HTTP/WebSocket
- **Stateless Handlers**: Use extracted state, avoid global mutability
- **Error Conversion**: Convert internal errors to proper HTTP responses
- **Real-time Events**: Use WebSocket for progress updates

## Tool Execution Rules
```rust
// ✅ Correct tool execution pattern
async fn execute_tool(tool: Arc<dyn Tool>, call: ToolCall) -> Result<ToolResult> {
    // Runtime handles actual IO
    let result = tool.call(call.args).await?;
    
    // Send progress via WebSocket if available
    if let Some(ws) = active_websockets.get(&session_id) {
        ws.send(ToolProgress::Started { tool_call: call.clone() }).await?;
    }
    
    Ok(result)
}
Session Management
In-Memory State: Active sessions in memory with RwLock

Persistence Optional: Support both ephemeral and persisted sessions

Cleanup: Implement session timeout and resource cleanup

Provider Integration
Multiple LLMs: Support OpenAI, Azure, Anthropic, local models

Configuration: Load API keys and endpoints from environment

Fallback: Implement retry logic and fallback providers

Security Notes
No Authentication: Assume trusted network (can add later)

Input Validation: Validate all API inputs

Resource Limits: Implement timeouts and size limits

text

## 4. Protocol Rules

```markdown:jamey-protocol/.cursorrules
# Jamey Protocol - Shared Type Definitions

## Design Principles
- **Serialization First**: All types must serialize to JSON cleanly
- **Backward Compatibility**: Use optional fields for evolution
- **Language Agnostic**: Types should be easily usable from other languages
- **Documentation**: Every field must have clear doc comments

## Type Rules
- **Use Enums**: For closed sets of values (roles, event types)
- **Avoid Tuples**: Use named structs for clarity
- **Optional Fields**: For all non-essential data
- **Stringly-Typed**: Avoid where possible, use enums instead

## Example Protocol Type
```rust
/// A message in a conversation between user and assistant
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Message {
    /// The role of the message sender
    pub role: Role,
    /// The content of the message
    pub content: String,
    /// Optional name for the sender (for multi-agent)
    pub name: Option<String>,
    /// Tool call ID for tool response messages
    pub tool_call_id: Option<String>,
}
Versioning Strategy
No Breaking Changes: Add, don't change or remove

Deprecation: Mark deprecated fields with comments

Validation: Consider adding JSON Schema for validation

Cross-Language Considerations
Field Names: Use snake_case for JSON compatibility

Simple Types: Prefer String, number, bool over complex types

No Rust-specific: Avoid Rust-specific features in protocol types

text

## 5. Client Rules

```markdown:jamey-cli/.cursorrules
# Jamey CLI - Thin Client Rules

## Architecture Rules
- **API Client Only**: No business logic, just HTTP calls to runtime
- **Stateless**: All state managed by runtime, CLI is dumb terminal
- **Error Display**: Convert API errors to user-friendly messages
- **Streaming Support**: Handle WebSocket events for real-time updates

## Allowed Dependencies
- `reqwest` (for HTTP client)
- `clap` (for command line parsing)
- `tokio` (for async runtime)
- `jamey-protocol` (for type definitions)

## Forbidden Dependencies
- `jamey-core` (business logic belongs to runtime)
- `jamey-tools` (tool execution belongs to runtime)
- Any LLM providers or tool implementations

## Correct Client Pattern
```rust
// ✅ Thin client - just makes API calls
impl RuntimeClient {
    pub async fn send_message(&self, session_id: &str, message: &str) -> Result<SendMessageResponse> {
        let request = SendMessageRequest {
            session_id: session_id.to_string(),
            message: message.to_string(),
        };
        
        let response = self.client
            .post(&format!("{}/sessions/{}/messages", self.base_url, session_id))
            .json(&request)
            .send()
            .await?;
            
        Ok(response.json().await?)
    }
}
User Experience
Immediate Feedback: Show typing indicators via WebSocket

Tool Progress: Display tool execution progress

Session Management: Handle session creation/selection gracefully

Error Recovery: Suggest actions when API calls fail

Command Structure
jamey-cli chat - Interactive chat session

jamey-cli create-session - Create new session

jamey-cli list-tools - Show available tools

jamey-cli status <session-id> - Show session status

text

## 6. Tool Implementation Rules

```markdown:jamey-tools/.cursorrules
# Jamey Tools - Implementation Rules

## Tool Categories
1. **System Tools**: Shell, filesystem, process management
2. **Enterprise Tools**: Outlook, Teams, security tools  
3. **External Tools**: HTTP endpoints, process plugins
4. **Development Tools**: Code analysis, testing, build tools

## Implementation Pattern
```rust
// ✅ Standard tool implementation
pub struct ShellTool {
    timeout: Duration,
    workspace: PathBuf,
}

#[async_trait]
impl Tool for ShellTool {
    fn name(&self) -> &str { "shell_exec" }

    async fn call(&self, args: Value) -> Result<ToolResult> {
        let cmd: String = serde_json::from_value(args.get("cmd").cloned().unwrap())?;
        
        let output = tokio::process::Command::new("bash")
            .arg("-c")
            .arg(&cmd)
            .current_dir(&self.workspace)
            .output()
            .await?;
            
        Ok(ToolResult {
            output: String::from_utf8_lossy(&output.stdout).to_string(),
            success: output.status.success(),
            // ...
        })
    }
}
Security Considerations
No Sanitization: Assume trusted environment as per requirements

Full Access: Tools have same permissions as runtime process

Resource Limits: Implement timeouts and memory limits

Error Handling: Don't crash on tool errors, return structured errors

Enterprise Tool Priorities
Microsoft Graph: Outlook emails, Teams messages, Calendar

Zscaler: Security status, threat monitoring

Rapid7: Vulnerability scanning, alert management

Windows COM: Office automation, system integration

External Tool Protocol
Process Tools: JSON over stdin/stdout

HTTP Tools: REST API endpoints

Configuration: Tool-specific config in runtime settings

Discovery: Runtime should auto-discover external tools

text

## 7. Provider Implementation Rules

```markdown:jamey-providers/.cursorrules
# Jamey Providers - LLM Integration Rules

## Supported Providers
1. **OpenAI**: GPT-4, GPT-3.5 Turbo
2. **Azure OpenAI**: Azure-hosted models
3. **Anthropic**: Claude models
4. **Ollama**: Local models
5. **LM Studio**: Local HTTP models

## Provider Trait Implementation
```rust
// ✅ Standard provider pattern
pub struct OpenAiProvider {
    client: reqwest::Client,
    api_key: String,
    base_url: String,
}

#[async_trait]
impl LlmProvider for OpenAiProvider {
    async fn chat(&self, messages: &[Message], tools: &[ToolSpec]) -> Result<LlmResponse> {
        let request = OpenAiRequest {
            model: self.model.clone(),
            messages: messages.to_vec(),
            tools: tools.to_vec(),
        };
        
        let response = self.client
            .post(&self.base_url)
            .header("Authorization", format!("Bearer {}", self.api_key))
            .json(&request)
            .send()
            .await?;
            
        // Parse OpenAI-specific response format
        self.parse_response(response).await
    }
}
Configuration Pattern
Environment Variables: API keys and endpoints

Runtime Config: Model selection, parameters

Fallback Chains: Multiple providers with fallback order

Rate Limiting: Respect API rate limits

Response Handling
Tool Calls: Extract structured tool calls from provider-specific format

Streaming: Support streaming responses where available

Error Recovery: Retry with exponential backoff

Token Counting: Track usage for cost monitoring

Model Parameters
Temperature: Configurable per session

Max Tokens: Enforce limits to control costs

Top P: Alternative to temperature

Stop Sequences: Provider-specific stopping conditions

text

## 8. Development Workflow Rules

```markdown:.github/cursorrules.md
# Development Workflow & Conventions

## Branch Strategy
- `main` - Stable releases only
- `develop` - Integration branch
- `feature/` - New features
- `fix/` - Bug fixes

## Commit Convention
<type>(<scope>): <description>

[optional body]

[optional footer]

text

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

## Testing Requirements
- **Core**: Unit tests for all pure logic
- **Runtime**: Integration tests for API endpoints
- **Tools**: Integration tests for tool execution
- **Clients**: E2E tests against mock runtime

## Code Review Checklist
- [ ] Pure core has no IO dependencies
- [ ] Protocol types are serializable
- [ ] Runtime API follows REST conventions
- [ ] Clients are thin (no business logic)
- [ ] Error handling is comprehensive
- [ ] Logging is appropriate
- [ ] Documentation is updated

## Performance Guidelines
- **Async Everywhere**: Use async/await for all IO
- **Avoid Blocking**: Use `tokio::spawn` for CPU-heavy tasks
- **Connection Pooling**: Reuse HTTP clients
- **Memory Efficiency**: Use streaming for large responses

## Security Notes (No Guardrails)
- **Trusted Environment**: Assume runtime has full system access
- **No Input Validation**: Tools receive raw arguments
- **No Rate Limiting**: Assume responsible usage
- **No Authentication**: Local runtime only

## Adding New Enterprise Tools
1. Define tool spec in `jamey-protocol`
2. Add pure interface in `jamey-core::tool_protocol` 
3. Implement actual tool in `jamey-tools`
4. Register tool in runtime startup
5. Update client tool listing
9. TUI Client Rules
markdown:jamey-tui/.cursorrules
# Jamey TUI - Terminal UI Rules

## Architecture
- **Ratatui Framework**: Use for terminal UI
- **WebSocket Client**: Real-time updates from runtime
- **Multi-pane Layout**: Chat, tools, status panels
- **Keyboard Navigation**: Vim-like keybindings

## UI Components
1. **Chat Panel**: Message history with syntax highlighting
2. **Tool Panel**: Active tool execution and progress
3. **Status Panel**: Session info, connected services
4. **Input Panel**: Multi-line message input

## Real-time Updates
```rust
// ✅ WebSocket event handling
async fn handle_websocket_events(websocket: WebSocketStream, ui_state: &mut UiState) {
    while let Some(message) = websocket.next().await {
        match message {
            Ok(WebSocketEvent::MessageUpdate { message }) => {
                ui_state.add_message(message);
            }
            Ok(WebSocketEvent::ToolCallStarted { tool_call }) => {
                ui_state.add_active_tool(tool_call);
            }
            // ... other events
        }
    }
}
Keybindings
Tab - Switch between panels

Ctrl+C - Exit application

Ctrl+R - Reload session

Ctrl+T - Focus tool panel

Esc - Cancel current action

Error Handling
Connection Loss: Auto-reconnect to runtime

API Errors: Show user-friendly error messages

Render Errors: Graceful degradation on terminal resize

text

These Cursor rules files will ensure consistent development across the entire Jamey Code project, maintaining the clean architecture separation while enabling rapid development of enterprise features. The rules enforce the "pure core + runtime daemon + thin clients" pattern that makes this a scalable, Claude Desktop-class platform.
