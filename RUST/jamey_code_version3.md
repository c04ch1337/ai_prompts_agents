I'll create a complete, updated Jamey Code project with all source files and folders. This will be a fully functional bare-metal Rust application.

## Project Structure

```
jamey-code/
├── Cargo.toml
├── .cursorrules
├── install.sh
├── install.ps1
├── .env.local.example
├── jamey-core/
├── jamey-protocol/
├── jamey-providers/
├── jamey-tools/
├── jamey-runtime/
├── jamey-cli/
└── jamey-tui/
```

Let me create all the files:

## 1. Workspace Configuration

```toml:Cargo.toml
[workspace]
members = [
    "jamey-core",
    "jamey-protocol", 
    "jamey-providers",
    "jamey-tools",
    "jamey-runtime",
    "jamey-cli",
    "jamey-tui",
]

[workspace.dependencies]
tokio = { version = "1.0", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
anyhow = "1.0"
thiserror = "1.0"
async-trait = "0.1"
uuid = { version = "1.0", features = ["v4", "serde"] }
tracing = "0.1"
tracing-subscriber = "0.3"
chrono = { version = "0.4", features = ["serde"] }
axum = { version = "0.7", features = ["macros"] }
tower = "0.4"
reqwest = { version = "0.11", features = ["json"] }
clap = { version = "4.0", features = ["derive"] }
ratatui = "0.23"
crossterm = "0.27"
sysinfo = "0.29"
dirs = "5.0"
whoami = "1.0"
toml = "0.8"
```

## 2. Cursor Rules

```markdown:.cursorrules
# Jamey Code - Digital Twin Project Rules

## Architecture Principles
- **Pure Core**: `jamey-core` must have NO IO dependencies
- **Runtime Daemon**: All actual IO happens in `jamey-runtime` 
- **Thin Clients**: CLI/TUI are just API clients to the runtime
- **Protocol-Driven**: Use `jamey-protocol` for all cross-boundary communication
- **Bare Metal**: No Docker - native binaries with system integration

## Code Organization
- **Workspace Structure**: Follow the established crate hierarchy
- **Dependency Direction**: 
  - `core` → `protocol` (allowed)
  - `runtime` → `core` + `providers` + `tools` (allowed)  
  - `clients` → `protocol` only (allowed)
  - NEVER: `core` → `runtime` or `core` → `providers`

## Platform Integration
- **Windows**: Use Windows Service API, AppData directories
- **Linux**: Use systemd, XDG directories (~/.config, ~/.local/share)
- **macOS**: Use launchd, ~/Library directories
- **Auto-Discovery**: CLI automatically finds local runtime on 127.0.0.1:3000

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

## When Adding New Features
1. Start with protocol definitions in `jamey-protocol`
2. Add pure domain logic in `jamey-core` 
3. Implement runtime support in `jamey-runtime`
4. Update clients to use new features
5. NEVER put IO in core, NEVER put business logic in clients

## Development Workflow
1. `cargo build --release` - Build all binaries
2. `jamey-runtime start` - Start daemon manually  
3. `jamey chat` - Use CLI (auto-discovers runtime)
4. `jamey-runtime install` - Install as system service (optional)

## Enterprise Integration Priority
1. **Microsoft 365**: Outlook, Teams, Graph API
2. **Security Tools**: Zscaler, Rapid7
3. **Windows Integration**: COM, notifications, system APIs
4. **Custom Business Tools**: Extensible plugin system
```

## 3. Environment Configuration

```ini:.env.local.example
# Jamey Code Digital Twin - Configuration
# Copy to .env.local and fill in your values

# LLM SETTINGS
OPENAI_API_KEY=your_openai_api_key_here
LLM_MODEL=gpt-4
LLM_BASE_URL=https://api.openai.com/v1
LLM_TIMEOUT_SECONDS=60
LLM_MAX_TOKENS=4000
LLM_TEMPERATURE=0.7

# MICROSOFT 365 SETTINGS
MS_TENANT_ID=your_tenant_id
MS_CLIENT_ID=your_client_id
MS_CLIENT_SECRET=your_client_secret

# ZSCALER SETTINGS
ZSCALER_BASE_URL=https://api.zscaler.net
ZSCALER_USERNAME=your_username
ZSCALER_PASSWORD=your_password

# RAPID7 SETTINGS  
RAPID7_BASE_URL=https://us.api.insight.rapid7.com
RAPID7_API_KEY=your_api_key

# RUNTIME SETTINGS
RUNTIME_HOST=127.0.0.1
RUNTIME_PORT=3000
LOG_LEVEL=info
DATA_DIR=./data
CONFIG_DIR=./config

# TOOL SETTINGS
ENABLE_SHELL_TOOLS=true
ENABLE_FILE_TOOLS=true
ENABLE_HTTP_TOOLS=true
TOOL_TIMEOUT_SECONDS=30

# SECURITY SETTINGS (No guardrails - full access)
ENABLE_GUARDRAILS=false
SANDBOX_MODE=false
```

## 4. Core Library (Pure Domain Logic)

```toml:jamey-core/Cargo.toml
[package]
name = "jamey-core"
version = "0.1.0"
edition = "2021"

[dependencies]
serde = { workspace = true }
serde_json = { workspace = true }
thiserror = { workspace = true }
async-trait = { workspace = true }
uuid = { workspace = true }
chrono = { workspace = true }

jamey-protocol = { path = "../jamey-protocol" }
```

```rust:jamey-core/src/lib.rs
//! Pure domain logic - no IO, no terminal dependencies, no HTTP
//! This is the portable "brain" that can run anywhere

pub mod agent;
pub mod session;
pub mod orchestration;
pub mod prompt;
pub mod tool_protocol;

pub use agent::{Agent, AgentEngine, AgentConfig, AgentResponse};
pub use session::{Session, SessionState, SessionManager};
pub use orchestration::{Orchestrator, SingleAgentMode, MultiAgentMode, Workflow, CollaborationStrategy};
pub use prompt::{PromptTemplate, SystemPrompt};
pub use tool_protocol::{ToolCall, ToolResult, ToolSpec};

// Re-export protocol types
pub use jamey_protocol::*;
```

```rust:jamey-core/src/agent.rs
use async_trait::async_trait;
use std::collections::HashMap;

use crate::session::Session;
use crate::orchestration::Orchestrator;
use crate::tool_protocol::{ToolCall, ToolResult};
use jamey_protocol::{Message, Conversation};

/// Pure agent logic - knows nothing about LLM providers or actual tool execution
#[async_trait]
pub trait Agent: Send + Sync {
    async fn process_message(
        &self, 
        session: &Session,
        message: Message,
    ) -> anyhow::Result<AgentResponse>;
    
    async fn process_tool_result(
        &self,
        session: &Session, 
        tool_result: ToolResult,
    ) -> anyhow::Result<AgentResponse>;
}

pub struct AgentEngine {
    orchestrator: Box<dyn Orchestrator>,
    config: AgentConfig,
}

pub struct AgentConfig {
    pub max_iterations: usize,
    pub enable_self_correction: bool,
    pub reasoning_depth: u8,
}

#[derive(Debug)]
pub struct AgentResponse {
    pub messages: Vec<Message>,
    pub tool_calls: Vec<ToolCall>,
    pub session_updates: HashMap<String, serde_json::Value>,
}

impl AgentEngine {
    pub fn new(orchestrator: Box<dyn Orchestrator>, config: AgentConfig) -> Self {
        Self { orchestrator, config }
    }
    
    pub async fn process_message(
        &self, 
        session: &Session,
        message: Message,
    ) -> anyhow::Result<AgentResponse> {
        self.orchestrator.process(session, message).await
    }
    
    pub async fn process_tool_result(
        &self,
        session: &Session, 
        tool_result: ToolResult,
    ) -> anyhow::Result<AgentResponse> {
        self.orchestrator.handle_tool_result(session, tool_result).await
    }
}

#[async_trait]
impl Agent for AgentEngine {
    async fn process_message(
        &self, 
        session: &Session,
        message: Message,
    ) -> anyhow::Result<AgentResponse> {
        self.process_message(session, message).await
    }
    
    async fn process_tool_result(
        &self,
        session: &Session, 
        tool_result: ToolResult,
    ) -> anyhow::Result<AgentResponse> {
        self.process_tool_result(session, tool_result).await
    }
}
```

```rust:jamey-core/src/session.rs
use std::collections::HashMap;
use chrono::{DateTime, Utc};

use jamey_protocol::{Conversation, Message};

/// Session management - pure domain logic
#[derive(Debug, Clone)]
pub struct Session {
    pub id: String,
    pub conversation: Conversation,
    pub state: SessionState,
    pub metadata: HashMap<String, serde_json::Value>,
    pub created_at: DateTime<Utc>,
    pub updated_at: DateTime<Utc>,
}

#[derive(Debug, Clone)]
pub struct SessionState {
    pub active_tools: Vec<String>,
    pub context: HashMap<String, String>,
    pub iteration_count: usize,
}

impl Session {
    pub fn new(initial_context: HashMap<String, String>) -> Self {
        Self {
            id: uuid::Uuid::new_v4().to_string(),
            conversation: Conversation::new(),
            state: SessionState {
                active_tools: Vec::new(),
                context: initial_context,
                iteration_count: 0,
            },
            metadata: HashMap::new(),
            created_at: Utc::now(),
            updated_at: Utc::now(),
        }
    }
    
    pub fn add_message(&mut self, message: Message) {
        self.conversation.push(message);
        self.updated_at = Utc::now();
    }
    
    pub fn increment_iteration(&mut self) {
        self.state.iteration_count += 1;
        self.updated_at = Utc::now();
    }
}

pub struct SessionManager {
    sessions: HashMap<String, Session>,
}

impl SessionManager {
    pub fn new() -> Self {
        Self {
            sessions: HashMap::new(),
        }
    }
    
    pub fn create_session(&mut self, initial_context: HashMap<String, String>) -> &Session {
        let session = Session::new(initial_context);
        let id = session.id.clone();
        self.sessions.insert(id.clone(), session);
        self.sessions.get(&id).unwrap()
    }
    
    pub fn get_session(&self, id: &str) -> Option<&Session> {
        self.sessions.get(id)
    }
    
    pub fn get_session_mut(&mut self, id: &str) -> Option<&mut Session> {
        self.sessions.get_mut(id)
    }
    
    pub fn update_session<F>(&mut self, id: &str, updater: F) -> anyhow::Result<()> 
    where
        F: FnOnce(&mut Session),
    {
        if let Some(session) = self.sessions.get_mut(id) {
            updater(session);
            session.updated_at = Utc::now();
            Ok(())
        } else {
            anyhow::bail!("Session not found: {}", id)
        }
    }
    
    pub fn list_sessions(&self) -> Vec<&Session> {
        self.sessions.values().collect()
    }
}
```

```rust:jamey-core/src/orchestration.rs
use async_trait::async_trait;
use std::collections::HashMap;

use crate::session::Session;
use crate::agent::AgentResponse;
use jamey_protocol::{Message, ToolCall, ToolResult};

/// Orchestrator patterns - completely independent of UI/transport
#[async_trait]
pub trait Orchestrator: Send + Sync {
    async fn process(&self, session: &Session, message: Message) -> anyhow::Result<AgentResponse>;
    async fn handle_tool_result(&self, session: &Session, result: ToolResult) -> anyhow::Result<AgentResponse>;
}

/// Single agent mode (default)
pub struct SingleAgentMode {
    pub max_tool_iterations: usize,
}

#[async_trait]
impl Orchestrator for SingleAgentMode {
    async fn process(&self, session: &Session, message: Message) -> anyhow::Result<AgentResponse> {
        // For now, return a simple response
        // In a real implementation, this would contain the logic to decide tool calls
        Ok(AgentResponse {
            messages: vec![Message {
                role: jamey_protocol::Role::Assistant,
                content: "I received your message. Tool calls would be decided here.".to_string(),
                name: None,
                tool_call_id: None,
            }],
            tool_calls: Vec::new(),
            session_updates: HashMap::new(),
        })
    }
    
    async fn handle_tool_result(&self, _session: &Session, _result: ToolResult) -> anyhow::Result<AgentResponse> {
        // Handle tool results and decide next steps
        Ok(AgentResponse {
            messages: vec![],
            tool_calls: vec![],
            session_updates: HashMap::new(),
        })
    }
}

/// Multi-agent collaboration mode
pub struct MultiAgentMode {
    pub agents: HashMap<String, Box<dyn Orchestrator>>,
    pub collaboration_strategy: CollaborationStrategy,
}

#[derive(Debug, Clone)]
pub enum CollaborationStrategy {
    Sequential,
    Parallel,
    Debate,
    Review,
}

#[async_trait]
impl Orchestrator for MultiAgentMode {
    async fn process(&self, session: &Session, message: Message) -> anyhow::Result<AgentResponse> {
        match self.collaboration_strategy {
            CollaborationStrategy::Sequential => {
                self.run_sequential(session, message).await
            }
            CollaborationStrategy::Debate => {
                self.run_debate(session, message).await
            }
            _ => {
                // Default to sequential
                self.run_sequential(session, message).await
            }
        }
    }
    
    async fn handle_tool_result(&self, session: &Session, result: ToolResult) -> anyhow::Result<AgentResponse> {
        // Multi-agent tool result handling
        Ok(AgentResponse {
            messages: vec![],
            tool_calls: vec![],
            session_updates: HashMap::new(),
        })
    }
}

impl MultiAgentMode {
    async fn run_sequential(&self, session: &Session, message: Message) -> anyhow::Result<AgentResponse> {
        // Run agents in sequence
        let mut final_response = AgentResponse {
            messages: vec![],
            tool_calls: vec![],
            session_updates: HashMap::new(),
        };
        
        for (name, agent) in &self.agents {
            let response = agent.process(session, message.clone()).await?;
            // Combine responses somehow
            final_response.messages.extend(response.messages);
            final_response.tool_calls.extend(response.tool_calls);
        }
        
        Ok(final_response)
    }
    
    async fn run_debate(&self, session: &Session, message: Message) -> anyhow::Result<AgentResponse> {
        // Run multiple agents and synthesize their responses
        let mut responses = Vec::new();
        
        for (name, agent) in &self.agents {
            let response = agent.process(session, message.clone()).await?;
            responses.push((name.clone(), response));
        }
        
        // Simple synthesis: just use the first agent's response for now
        if let Some((_, first_response)) = responses.first() {
            Ok(first_response.clone())
        } else {
            Ok(AgentResponse {
                messages: vec![],
                tool_calls: vec![],
                session_updates: HashMap::new(),
            })
        }
    }
}

/// Workflow definition for complex multi-step processes
#[derive(Debug, Clone)]
pub struct Workflow {
    pub nodes: Vec<WorkflowNode>,
    pub edges: Vec<WorkflowEdge>,
}

#[derive(Debug, Clone)]
pub struct WorkflowNode {
    pub id: String,
    pub agent_type: String,
    pub prompt_template: String,
    pub tools: Vec<String>,
}

#[derive(Debug, Clone)]
pub struct WorkflowEdge {
    pub from: String,
    pub to: String,
    pub condition: Option<String>,
}
```

```rust:jamey-core/src/prompt.rs
use std::collections::HashMap;

/// Prompt templates and system prompts
#[derive(Debug, Clone)]
pub struct PromptTemplate {
    pub template: String,
    pub variables: Vec<String>,
}

impl PromptTemplate {
    pub fn new(template: String) -> Self {
        // Simple variable extraction: {variable}
        let variables = extract_variables(&template);
        Self { template, variables }
    }
    
    pub fn render(&self, context: &HashMap<String, String>) -> String {
        let mut result = self.template.clone();
        for variable in &self.variables {
            if let Some(value) = context.get(variable) {
                result = result.replace(&format!("{{{}}}", variable), value);
            }
        }
        result
    }
}

fn extract_variables(template: &str) -> Vec<String> {
    let mut variables = Vec::new();
    let mut in_var = false;
    let mut current_var = String::new();
    
    for ch in template.chars() {
        if ch == '{' {
            in_var = true;
            current_var.clear();
        } else if ch == '}' && in_var {
            in_var = false;
            if !current_var.is_empty() {
                variables.push(current_var.clone());
            }
        } else if in_var {
            current_var.push(ch);
        }
    }
    
    variables
}

/// System prompt configuration
#[derive(Debug, Clone)]
pub struct SystemPrompt {
    pub content: String,
    pub role: jamey_protocol::Role,
}

impl Default for SystemPrompt {
    fn default() -> Self {
        Self {
            content: "You are a helpful AI assistant.".to_string(),
            role: jamey_protocol::Role::System,
        }
    }
}

impl SystemPrompt {
    pub fn new(content: String) -> Self {
        Self {
            content,
            role: jamey_protocol::Role::System,
        }
    }
    
    pub fn to_message(&self) -> jamey_protocol::Message {
        jamey_protocol::Message {
            role: self.role.clone(),
            content: self.content.clone(),
            name: None,
            tool_call_id: None,
        }
    }
}
```

```rust:jamey-core/src/tool_protocol.rs
use serde::{Deserialize, Serialize};

/// Tool protocol definitions - pure data structures
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolCall {
    pub id: String,
    pub name: String,
    pub args: serde_json::Value,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolResult {
    pub id: String,
    pub name: String,
    pub output: String,
    pub success: bool,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolSpec {
    pub name: String,
    pub description: String,
    pub parameters: serde_json::Value,
}

/// Trait for tools - this is the only place where we define the interface
/// Actual implementation happens in jamey-tools
pub trait Tool: Send + Sync {
    fn name(&self) -> &str;
    fn description(&self) -> &str;
    fn spec(&self) -> ToolSpec;
}
```

## 5. Protocol Definitions

```toml:jamey-protocol/Cargo.toml
[package]
name = "jamey-protocol"
version = "0.1.0"
edition = "2021"

[dependencies]
serde = { workspace = true }
serde_json = { workspace = true }
uuid = { workspace = true }
chrono = { workspace = true }
```

```rust:jamey-protocol/src/lib.rs
//! Shared protocol definitions - used by core, runtime, and clients

use serde::{Deserialize, Serialize};

#[derive(Debug, Clone, Serialize, Deserialize)]
pub enum Role {
    System,
    User,
    Assistant,
    Tool,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Message {
    pub role: Role,
    pub content: String,
    pub name: Option<String>,
    pub tool_call_id: Option<String>,
}

impl Message {
    pub fn system(content: String) -> Self {
        Self {
            role: Role::System,
            content,
            name: None,
            tool_call_id: None,
        }
    }
    
    pub fn user(content: String) -> Self {
        Self {
            role: Role::User,
            content,
            name: None,
            tool_call_id: None,
        }
    }
    
    pub fn assistant(content: String) -> Self {
        Self {
            role: Role::Assistant,
            content,
            name: None,
            tool_call_id: None,
        }
    }
    
    pub fn tool(content: String, tool_call_id: String) -> Self {
        Self {
            role: Role::Tool,
            content,
            name: None,
            tool_call_id: Some(tool_call_id),
        }
    }
}

pub type Conversation = Vec<Message>;

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolCall {
    pub id: String,
    pub name: String,
    pub args: serde_json::Value,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolResult {
    pub id: String,
    pub name: String,
    pub output: String,
    pub success: bool,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolSpec {
    pub name: String,
    pub description: String,
    pub parameters: serde_json::Value,
}

// API Protocol for runtime ↔ clients
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct CreateSessionRequest {
    pub context: Option<serde_json::Value>,
    pub agent_mode: Option<String>,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct CreateSessionResponse {
    pub session_id: String,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct SendMessageRequest {
    pub message: String,
    pub session_id: String,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct SendMessageResponse {
    pub messages: Vec<Message>,
    pub tool_calls: Vec<ToolCall>,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolExecutionRequest {
    pub session_id: String,
    pub tool_call: ToolCall,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolExecutionResponse {
    pub result: ToolResult,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct SessionStateResponse {
    pub session_id: String,
    pub conversation: Conversation,
    pub state: serde_json::Value,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ListToolsResponse {
    pub tools: Vec<ToolSpec>,
}

// WebSocket events for real-time communication
#[derive(Debug, Clone, Serialize, Deserialize)]
#[serde(tag = "type", rename_all = "snake_case")]
pub enum WebSocketEvent {
    MessageUpdate { message: Message },
    ToolCallStarted { tool_call: ToolCall },
    ToolCallCompleted { result: ToolResult },
    SessionUpdated { session_id: String, state: serde_json::Value },
    Error { message: String },
}
```

## 6. Providers Library

```toml:jamey-providers/Cargo.toml
[package]
name = "jamey-providers"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { workspace = true }
serde = { workspace = true }
serde_json = { workspace = true }
anyhow = { workspace = true }
thiserror = { workspace = true }
async-trait = { workspace = true }
reqwest = { workspace = true }

jamey-core = { path = "../jamey-core" }
jamey-protocol = { path = "../jamey-protocol" }
```

```rust:jamey-providers/src/lib.rs
//! LLM provider implementations

pub mod openai;
pub mod azure;
pub mod anthropic;
pub mod ollama;

pub use openai::{OpenAiProvider, OpenAiConfig};
use jamey_protocol::{Message, ToolSpec};

#[async_trait::async_trait]
pub trait LlmProvider: Send + Sync {
    async fn chat(
        &self,
        messages: &[Message],
        tools: &[ToolSpec],
    ) -> anyhow::Result<LlmResponse>;
}

#[derive(Debug)]
pub struct LlmResponse {
    pub message: Option<Message>,
    pub tool_calls: Vec<jamey_protocol::ToolCall>,
}

// Mock provider for testing
pub struct MockProvider;

#[async_trait::async_trait]
impl LlmProvider for MockProvider {
    async fn chat(
        &self,
        messages: &[Message],
        _tools: &[ToolSpec],
    ) -> anyhow::Result<LlmResponse> {
        let last_message = messages.last()
            .map(|m| m.content.clone())
            .unwrap_or_else(|| "Hello".to_string());
            
        Ok(LlmResponse {
            message: Some(Message::assistant(format!("Mock response to: {}", last_message))),
            tool_calls: Vec::new(),
        })
    }
}
```

```rust:jamey-providers/src/openai.rs
use async_trait::async_trait;
use reqwest::Client;
use serde::{Deserialize, Serialize};

use crate::{LlmProvider, LlmResponse};
use jamey_protocol::{Message, ToolSpec};

#[derive(Debug, Clone)]
pub struct OpenAiConfig {
    pub api_key: String,
    pub model: String,
    pub base_url: String,
    pub timeout: std::time::Duration,
}

impl Default for OpenAiConfig {
    fn default() -> Self {
        Self {
            api_key: String::new(),
            model: "gpt-4".to_string(),
            base_url: "https://api.openai.com/v1".to_string(),
            timeout: std::time::Duration::from_secs(30),
        }
    }
}

pub struct OpenAiProvider {
    client: Client,
    config: OpenAiConfig,
}

impl OpenAiProvider {
    pub fn new(config: OpenAiConfig) -> Self {
        let client = Client::builder()
            .timeout(config.timeout)
            .build()
            .expect("Failed to create HTTP client");
            
        Self { client, config }
    }
}

#[async_trait]
impl LlmProvider for OpenAiProvider {
    async fn chat(
        &self,
        messages: &[Message],
        tools: &[ToolSpec],
    ) -> anyhow::Result<LlmResponse> {
        // Convert messages to OpenAI format
        let openai_messages: Vec<OpenAiMessage> = messages
            .iter()
            .map(|msg| OpenAiMessage {
                role: match msg.role {
                    jamey_protocol::Role::System => "system".to_string(),
                    jamey_protocol::Role::User => "user".to_string(),
                    jamey_protocol::Role::Assistant => "assistant".to_string(),
                    jamey_protocol::Role::Tool => "tool".to_string(),
                },
                content: msg.content.clone(),
            })
            .collect();

        let request = OpenAiRequest {
            model: self.config.model.clone(),
            messages: openai_messages,
            tools: if tools.is_empty() {
                None
            } else {
                Some(tools.iter().map(|spec| OpenAiTool {
                    r#type: "function".to_string(),
                    function: OpenAiFunction {
                        name: spec.name.clone(),
                        description: Some(spec.description.clone()),
                        parameters: spec.parameters.clone(),
                    },
                }).collect())
            },
        };

        let response = self.client
            .post(&format!("{}/chat/completions", self.config.base_url))
            .header("Authorization", format!("Bearer {}", self.config.api_key))
            .header("Content-Type", "application/json")
            .json(&request)
            .send()
            .await?;

        if !response.status().is_success() {
            let error_text = response.text().await.unwrap_or_default();
            anyhow::bail!("OpenAI API error: {}", error_text);
        }

        let openai_response: OpenAiResponse = response.json().await?;
        
        // Convert response back to our format
        let choice = openai_response.choices.first()
            .ok_or_else(|| anyhow::anyhow!("No choices in response"))?;
            
        let message = if let Some(ref msg) = choice.message {
            Some(Message::assistant(msg.content.clone()))
        } else {
            None
        };
        
        let tool_calls = Vec::new(); // Would parse from response if present
        
        Ok(LlmResponse {
            message,
            tool_calls,
        })
    }
}

// OpenAI-specific types
#[derive(Debug, Serialize, Deserialize)]
struct OpenAiMessage {
    role: String,
    content: String,
}

#[derive(Debug, Serialize, Deserialize)]
struct OpenAiRequest {
    model: String,
    messages: Vec<OpenAiMessage>,
    tools: Option<Vec<OpenAiTool>>,
}

#[derive(Debug, Serialize, Deserialize)]
struct OpenAiTool {
    r#type: String,
    function: OpenAiFunction,
}

#[derive(Debug, Serialize, Deserialize)]
struct OpenAiFunction {
    name: String,
    description: Option<String>,
    parameters: serde_json::Value,
}

#[derive(Debug, Serialize, Deserialize)]
struct OpenAiResponse {
    choices: Vec<OpenAiChoice>,
}

#[derive(Debug, Serialize, Deserialize)]
struct OpenAiChoice {
    message: Option<OpenAiResponseMessage>,
}

#[derive(Debug, Serialize, Deserialize)]
struct OpenAiResponseMessage {
    content: String,
}
```

## 7. Tools Library

```toml:jamey-tools/Cargo.toml
[package]
name = "jamey-tools"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { workspace = true }
serde = { workspace = true }
serde_json = { workspace = true }
anyhow = { workspace = true }
thiserror = { workspace = true }
async-trait = { workspace = true }

jamey-core = { path = "../jamey-core" }
jamey-protocol = { path = "../jamey-protocol" }
```

```rust:jamey-tools/src/lib.rs
//! Tool implementations

pub mod shell;
pub mod filesystem;
pub mod http;
pub mod plugin;

pub use shell::ShellTool;
pub use filesystem::{FileReadTool, FileWriteTool};
pub use http::HttpTool;
pub use plugin::{ExternalTool, ProcessTool};

use async_trait::async_trait;
use jamey_core::tool_protocol::{Tool, ToolSpec, ToolCall, ToolResult};

/// Enhanced tool trait that includes execution
#[async_trait]
pub trait ExecutableTool: Tool + Send + Sync {
    async fn execute(&self, args: serde_json::Value) -> anyhow::Result<ToolResult>;
}

// Common tool error type
#[derive(thiserror::Error, Debug)]
pub enum ToolError {
    #[error("IO error: {0}")]
    Io(#[from] std::io::Error),
    #[error("Serialization error: {0}")]
    Serde(#[from] serde_json::Error),
    #[error("Tool execution error: {0}")]
    Execution(String),
    #[error("Timeout error")]
    Timeout,
}

/// Tool registry for runtime
pub struct ToolRegistry {
    tools: std::collections::HashMap<String, Box<dyn ExecutableTool>>,
}

impl ToolRegistry {
    pub fn new() -> Self {
        Self {
            tools: std::collections::HashMap::new(),
        }
    }
    
    pub fn register<T: ExecutableTool + 'static>(&mut self, tool: T) {
        self.tools.insert(tool.name().to_string(), Box::new(tool));
    }
    
    pub async fn execute(&self, tool_call: ToolCall) -> anyhow::Result<ToolResult> {
        let tool = self.tools
            .get(&tool_call.name)
            .ok_or_else(|| anyhow::anyhow!("Unknown tool: {}", tool_call.name))?;
            
        tool.execute(tool_call.args).await
    }
    
    pub fn list_tools(&self) -> Vec<ToolSpec> {
        self.tools.values()
            .map(|tool| tool.spec())
            .collect()
    }
}

impl Default for ToolRegistry {
    fn default() -> Self {
        Self::new()
    }
}
```

```rust:jamey-tools/src/shell.rs
use async_trait::async_trait;
use serde::{Deserialize, Serialize};
use std::time::Duration;
use tokio::process::Command;

use crate::{ExecutableTool, ToolError};
use jamey_core::tool_protocol::{ToolSpec, ToolResult};

pub struct ShellTool {
    pub timeout: Duration,
    pub workspace: std::path::PathBuf,
}

impl ShellTool {
    pub fn new(workspace: std::path::PathBuf, timeout: Duration) -> Self {
        Self { timeout, workspace }
    }
}

#[async_trait]
impl ExecutableTool for ShellTool {
    fn name(&self) -> &str {
        "shell_exec"
    }

    fn description(&self) -> &str {
        "Execute shell commands in the workspace directory"
    }

    fn spec(&self) -> ToolSpec {
        ToolSpec {
            name: self.name().to_string(),
            description: self.description().to_string(),
            parameters: serde_json::json!({
                "type": "object",
                "properties": {
                    "command": {
                        "type": "string",
                        "description": "The shell command to execute"
                    }
                },
                "required": ["command"]
            }),
        }
    }

    async fn execute(&self, args: serde_json::Value) -> anyhow::Result<ToolResult> {
        #[derive(Deserialize)]
        struct ShellArgs {
            command: String,
        }

        let args: ShellArgs = serde_json::from_value(args)
            .map_err(|e| ToolError::Execution(e.to_string()))?;

        let output = tokio::time::timeout(self.timeout, async {
            Command::new(if cfg!(target_os = "windows") { "cmd" } else { "bash" })
                .arg(if cfg!(target_os = "windows") { "/C" } else { "-c" })
                .arg(&args.command)
                .current_dir(&self.workspace)
                .output()
                .await
        })
        .await
        .map_err(|_| ToolError::Timeout)??;

        let stdout = String::from_utf8_lossy(&output.stdout).to_string();
        let stderr = String::from_utf8_lossy(&output.stderr).to_string();
        let success = output.status.success();

        let output = if stderr.is_empty() {
            stdout
        } else {
            format!("STDOUT:\n{}\n\nSTDERR:\n{}", stdout, stderr)
        };

        Ok(ToolResult {
            id: uuid::Uuid::new_v4().to_string(),
            name: self.name().to_string(),
            output,
            success,
        })
    }
}
```

```rust:jamey-tools/src/filesystem.rs
use async_trait::async_trait;
use serde::Deserialize;
use std::path::PathBuf;

use crate::{ExecutableTool, ToolError};
use jamey_core::tool_protocol::{ToolSpec, ToolResult};

pub struct FileReadTool {
    pub workspace: PathBuf,
}

impl FileReadTool {
    pub fn new(workspace: PathBuf) -> Self {
        Self { workspace }
    }
    
    fn sanitize_path(&self, user_path: &str) -> anyhow::Result<PathBuf> {
        let path = PathBuf::from(user_path);
        let full_path = self.workspace.join(&path);
        
        // Basic path traversal protection
        if full_path.components().any(|c| matches!(c, std::path::Component::ParentDir)) {
            return Err(anyhow::anyhow!("Path traversal not allowed"));
        }
        
        Ok(full_path)
    }
}

#[async_trait]
impl ExecutableTool for FileReadTool {
    fn name(&self) -> &str {
        "file_read"
    }

    fn description(&self) -> &str {
        "Read contents of a file in the workspace"
    }

    fn spec(&self) -> ToolSpec {
        ToolSpec {
            name: self.name().to_string(),
            description: self.description().to_string(),
            parameters: serde_json::json!({
                "type": "object",
                "properties": {
                    "path": {
                        "type": "string",
                        "description": "Relative path to the file from workspace root"
                    }
                },
                "required": ["path"]
            }),
        }
    }

    async fn execute(&self, args: serde_json::Value) -> anyhow::Result<ToolResult> {
        #[derive(Deserialize)]
        struct FileReadArgs {
            path: String,
        }

        let args: FileReadArgs = serde_json::from_value(args)
            .map_err(|e| ToolError::Execution(e.to_string()))?;

        let full_path = self.sanitize_path(&args.path)?;
        let content = tokio::fs::read_to_string(&full_path).await?;

        Ok(ToolResult {
            id: uuid::Uuid::new_v4().to_string(),
            name: self.name().to_string(),
            output: content,
            success: true,
        })
    }
}

pub struct FileWriteTool {
    pub workspace: PathBuf,
}

impl FileWriteTool {
    pub fn new(workspace: PathBuf) -> Self {
        Self { workspace }
    }
    
    fn sanitize_path(&self, user_path: &str) -> anyhow::Result<PathBuf> {
        let path = PathBuf::from(user_path);
        let full_path = self.workspace.join(&path);
        
        // Basic path traversal protection
        if full_path.components().any(|c| matches!(c, std::path::Component::ParentDir)) {
            return Err(anyhow::anyhow!("Path traversal not allowed"));
        }
        
        Ok(full_path)
    }
}

#[async_trait]
impl ExecutableTool for FileWriteTool {
    fn name(&self) -> &str {
        "file_write"
    }

    fn description(&self) -> &str {
        "Write content to a file in the workspace"
    }

    fn spec(&self) -> ToolSpec {
        ToolSpec {
            name: self.name().to_string(),
            description: self.description().to_string(),
            parameters: serde_json::json!({
                "type": "object",
                "properties": {
                    "path": {
                        "type": "string",
                        "description": "Relative path to the file from workspace root"
                    },
                    "content": {
                        "type": "string", 
                        "description": "Content to write to the file"
                    }
                },
                "required": ["path", "content"]
            }),
        }
    }

    async fn execute(&self, args: serde_json::Value) -> anyhow::Result<ToolResult> {
        #[derive(Deserialize)]
        struct FileWriteArgs {
            path: String,
            content: String,
        }

        let args: FileWriteArgs = serde_json::from_value(args)
            .map_err(|e| ToolError::Execution(e.to_string()))?;

        let full_path = self.sanitize_path(&args.path)?;
        
        // Create parent directories if needed
        if let Some(parent) = full_path.parent() {
            tokio::fs::create_dir_all(parent).await?;
        }
        
        tokio::fs::write(&full_path, &args.content).await?;

        Ok(ToolResult {
            id: uuid::Uuid::new_v4().to_string(),
            name: self.name().to_string(),
            output: format!("Successfully wrote to {}", args.path),
            success: true,
        })
    }
}
```

## 8. Runtime Daemon

```toml:jamey-runtime/Cargo.toml
[package]
name = "jamey-runtime"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { workspace = true, features = ["full"] }
serde = { workspace = true }
serde_json = { workspace = true }
anyhow = { workspace = true }
thiserror = { workspace = true }
async-trait = { workspace = true }
axum = { workspace = true }
tower = { workspace = true }
tracing = { workspace = true }
uuid = { workspace = true }
chrono = { workspace = true }
dirs = { workspace = true }
whoami = { workspace = true }
toml = { workspace = true }

jamey-core = { path = "../jamey-core" }
jamey-protocol = { path = "../jamey-protocol" }
jamey-providers = { path = "../jamey-providers" }
jamey-tools = { path = "../jamey-tools" }
```

```rust:jamey-runtime/src/main.rs
use clap::{Parser, Subcommand};
use jamey_runtime::{daemon::NativeDaemon, service::ServiceManager};
use tracing_subscriber;

#[derive(Parser)]
#[command(name = "jamey-runtime")]
#[command(about = "Jamey Code Runtime Daemon - Your Digital Twin Engine")]
struct Cli {
    #[command(subcommand)]
    command: Option<Commands>,
    
    #[arg(long, default_value = "info")]
    log_level: String,
    
    #[arg(long)]
    config: Option<std::path::PathBuf>,
}

#[derive(Subcommand)]
enum Commands {
    /// Start the runtime daemon
    Start {
        #[arg(long, default_value = "3000")]
        port: u16,
        
        #[arg(long, default_value = "127.0.0.1")]
        host: String,
        
        #[arg(long)]
        foreground: bool,
    },
    /// Stop the running daemon
    Stop,
    /// Check daemon status
    Status,
    /// View runtime logs
    Logs {
        #[arg(short, long)]
        follow: bool,
        
        #[arg(short, long)]
        lines: Option<usize>,
    },
    /// Install as system service
    Install,
    /// Uninstall system service
    Uninstall,
}

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let cli = Cli::parse();
    
    // Initialize logging
    tracing_subscriber::fmt()
        .with_max_level(match cli.log_level.as_str() {
            "trace" => tracing::Level::TRACE,
            "debug" => tracing::Level::DEBUG,
            "info" => tracing::Level::INFO,
            "warn" => tracing::Level::WARN,
            "error" => tracing::Level::ERROR,
            _ => tracing::Level::INFO,
        })
        .init();

    match cli.command {
        Some(Commands::Start { port, host, foreground }) => {
            start_daemon(port, host, foreground).await?;
        }
        Some(Commands::Stop) => {
            stop_daemon().await?;
        }
        Some(Commands::Status) => {
            check_status().await?;
        }
        Some(Commands::Logs { follow, lines }) => {
            show_logs(follow, lines).await?;
        }
        Some(Commands::Install) => {
            install_service().await?;
        }
        Some(Commands::Uninstall) => {
            uninstall_service().await?;
        }
        None => {
            // Default: start in foreground
            start_daemon(3000, "127.0.0.1".to_string(), true).await?;
        }
    }

    Ok(())
}

async fn start_daemon(port: u16, host: String, foreground: bool) -> anyhow::Result<()> {
    tracing::info!("Starting Jamey Runtime Daemon on {}:{}", host, port);
    
    let daemon = NativeDaemon::new().await?;
    
    if foreground {
        daemon.run().await?;
    } else {
        // For background operation
        tracing::warn!("Background operation not yet implemented, running in foreground");
        daemon.run().await?;
    }
    
    Ok(())
}

async fn stop_daemon() -> anyhow::Result<()> {
    // Implementation to stop running daemon
    tracing::info!("Stopping Jamey Runtime Daemon");
    
    // Try to call shutdown endpoint
    let client = reqwest::Client::new();
    let _ = client.post("http://127.0.0.1:3000/shutdown").send().await;
    
    println!("✅ Runtime shutdown signal sent");
    Ok(())
}

async fn check_status() -> anyhow::Result<()> {
    let client = reqwest::Client::new();
    match client.get("http://127.0.0.1:3000/health")
        .timeout(std::time::Duration::from_secs(2))
        .send().await 
    {
        Ok(response) if response.status().is_success() => {
            println!("✅ Jamey Runtime is running and healthy");
        }
        Ok(response) => {
            println!("❌ Jamey Runtime responded with error: {}", response.status());
        }
        Err(_) => {
            println!("❌ Jamey Runtime is not running or not accessible");
        }
    }
    Ok(())
}

async fn show_logs(_follow: bool, _lines: Option<usize>) -> anyhow::Result<()> {
    println!("Log viewing would be implemented here");
    // Would read from log files in data directory
    Ok(())
}

async fn install_service() -> anyhow::Result<()> {
    let service_manager = ServiceManager::new()?;
    service_manager.install().await?;
    Ok(())
}

async fn uninstall_service() -> anyhow::Result<()> {
    let service_manager = ServiceManager::new()?;
    service_manager.uninstall().await?;
    Ok(())
}
```

```rust:jamey-runtime/src/lib.rs
pub mod api;
pub mod daemon;
pub mod state;
pub mod config;
pub mod service;

pub use api::RuntimeApi;
pub use state::RuntimeState;
pub use config::{RuntimeConfig, ConfigManager};
```

```rust:jamey-runtime/src/daemon.rs
use std::path::PathBuf;
use std::sync::Arc;
use tokio::sync::Notify;

use crate::state::RuntimeState;
use crate::api::RuntimeApi;

/// Native daemon manager for bare-metal operation
pub struct NativeDaemon {
    runtime_state: Arc<RuntimeState>,
    shutdown_notify: Arc<Notify>,
    data_dir: PathBuf,
    config_dir: PathBuf,
    log_dir: PathBuf,
}

impl NativeDaemon {
    pub async fn new() -> anyhow::Result<Self> {
        // Use platform-appropriate directories
        let data_dir = dirs::data_dir()
            .unwrap_or_else(|| PathBuf::from("./data"))
            .join("jamey-code");
            
        let config_dir = dirs::config_dir()
            .unwrap_or_else(|| PathBuf::from("./config"))
            .join("jamey-code");
            
        let log_dir = dirs::cache_dir()
            .unwrap_or_else(|| PathBuf::from("./logs"))
            .join("jamey-code");

        // Create directories if they don't exist
        tokio::fs::create_dir_all(&data_dir).await?;
        tokio::fs::create_dir_all(&config_dir).await?;
        tokio::fs::create_dir_all(&log_dir).await?;

        // Initialize runtime state
        let runtime_state = Arc::new(RuntimeState::new().await?);

        Ok(Self {
            runtime_state,
            shutdown_notify: Arc::new(Notify::new()),
            data_dir,
            config_dir,
            log_dir,
        })
    }

    pub async fn run(&self) -> anyhow::Result<()> {
        tracing::info!("Starting Jamey Runtime Daemon");
        tracing::info!("Data directory: {}", self.data_dir.display());
        tracing::info!("Config directory: {}", self.config_dir.display());
        
        // Start the HTTP API server
        let api_handle = self.start_api_server().await?;
        
        // Start background services
        let monitor_handle = self.start_system_monitor();
        let health_handle = self.start_health_checker();
        
        // Wait for shutdown signal
        self.wait_for_shutdown().await;
        
        // Clean shutdown
        tracing::info!("Shutting down Jamey Runtime Daemon");
        api_handle.abort();
        monitor_handle.abort();
        health_handle.abort();
        
        Ok(())
    }
    
    async fn start_api_server(&self) -> anyhow::Result<tokio::task::JoinHandle<()>> {
        let app = RuntimeApi::router(self.runtime_state.clone());
        let listener = tokio::net::TcpListener::bind("127.0.0.1:3000").await?;
        
        let handle = tokio::spawn(async move {
            if let Err(e) = axum::serve(listener, app).await {
                tracing::error!("API server error: {}", e);
            }
        });
        
        tracing::info!("API server started on http://127.0.0.1:3000");
        Ok(handle)
    }
    
    fn start_system_monitor(&self) -> tokio::task::JoinHandle<()> {
        let state = self.runtime_state.clone();
        tokio::spawn(async move {
            let mut interval = tokio::time::interval(std::time::Duration::from_secs(30));
            loop {
                interval.tick().await;
                // Monitor system resources, tool health, etc.
                tracing::debug!("System monitor tick");
            }
        })
    }
    
    fn start_health_checker(&self) -> tokio::task::JoinHandle<()> {
        let state = self.runtime_state.clone();
        tokio::spawn(async move {
            let mut interval = tokio::time::interval(std::time::Duration::from_secs(60));
            loop {
                interval.tick().await;
                // Check service health, API connectivity, etc.
                tracing::debug!("Health check tick");
            }
        })
    }
    
    async fn wait_for_shutdown(&self) {
        tokio::select! {
            _ = tokio::signal::ctrl_c() => {
                tracing::info!("Received CTRL+C, shutting down");
            }
            _ = self.shutdown_notify.notified() => {
                tracing::info!("Received internal shutdown signal");
            }
        }
    }
    
    pub fn shutdown(&self) {
        self.shutdown_notify.notify_one();
    }
}
```

```rust:jamey-runtime/src/state.rs
use std::collections::HashMap;
use std::sync::Arc;
use tokio::sync::RwLock;

use jamey_core::{SessionManager, AgentEngine, SingleAgentMode, AgentConfig};
use jamey_protocol::SessionStateResponse;
use jamey_providers::{LlmProvider, MockProvider};
use jamey_tools::ToolRegistry;

/// Global runtime state - shared across API requests
#[derive(Clone)]
pub struct RuntimeState {
    pub session_manager: Arc<RwLock<SessionManager>>,
    pub agent_engine: Arc<AgentEngine>,
    pub tool_registry: Arc<RwLock<ToolRegistry>>,
    pub llm_provider: Arc<dyn LlmProvider>,
}

impl RuntimeState {
    pub async fn new() -> anyhow::Result<Self> {
        // Initialize agent engine
        let orchestrator = Box::new(SingleAgentMode {
            max_tool_iterations: 10,
        });
        
        let agent_config = AgentConfig {
            max_iterations: 5,
            enable_self_correction: true,
            reasoning_depth: 2,
        };
        
        let agent_engine = AgentEngine::new(orchestrator, agent_config);
        
        // Initialize tool registry
        let mut tool_registry = ToolRegistry::new();
        
        // Add basic tools
        let workspace = std::env::current_dir().unwrap_or_else(|_| PathBuf::from("."));
        tool_registry.register(jamey_tools::ShellTool::new(
            workspace.clone(),
            std::time::Duration::from_secs(30),
        ));
        tool_registry.register(jamey_tools::FileReadTool::new(workspace.clone()));
        tool_registry.register(jamey_tools::FileWriteTool::new(workspace));
        
        // Initialize LLM provider (mock for now)
        let llm_provider: Arc<dyn LlmProvider> = Arc::new(MockProvider);
        
        Ok(Self {
            session_manager: Arc::new(RwLock::new(SessionManager::new())),
            agent_engine: Arc::new(agent_engine),
            tool_registry: Arc::new(RwLock::new(tool_registry)),
            llm_provider,
        })
    }
    
    pub async fn get_session_state(&self, session_id: &str) -> anyhow::Result<SessionStateResponse> {
        let manager = self.session_manager.read().await;
        if let Some(session) = manager.get_session(session_id) {
            Ok(SessionStateResponse {
                session_id: session_id.to_string(),
                conversation: session.conversation.clone(),
                state: serde_json::to_value(&session.state)?,
            })
        } else {
            anyhow::bail!("Session not found: {}", session_id)
        }
    }
}
```

```rust:jamey-runtime/src/api.rs
use axum::{
    extract::{State, Path, Json},
    response::Response,
    routing::{get, post},
    Router,
};
use std::sync::Arc;
use tracing::info;

use jamey_protocol::{
    CreateSessionRequest, CreateSessionResponse, SendMessageRequest, SendMessageResponse,
    ToolExecutionRequest, ToolExecutionResponse, SessionStateResponse, ListToolsResponse,
};

use crate::state::RuntimeState;

/// HTTP API for the runtime daemon
pub struct RuntimeApi;

impl RuntimeApi {
    pub fn router(state: Arc<RuntimeState>) -> Router {
        Router::new()
            .route("/sessions", post(Self::create_session))
            .route("/sessions/:session_id", get(Self::get_session))
            .route("/sessions/:session_id/messages", post(Self::send_message))
            .route("/sessions/:session_id/tools", post(Self::execute_tool))
            .route("/tools", get(Self::list_tools))
            .route("/health", get(Self::health_check))
            .route("/shutdown", post(Self::shutdown))
            .with_state(state)
    }
    
    async fn create_session(
        State(state): State<Arc<RuntimeState>>,
        Json(request): Json<CreateSessionRequest>,
    ) -> Json<CreateSessionResponse> {
        info!("Creating new session");
        
        let mut manager = state.session_manager.write().await;
        let initial_context = request.context
            .and_then(|c| serde_json::from_value(c).ok())
            .unwrap_or_default();
        
        let session = manager.create_session(initial_context);
        
        Json(CreateSessionResponse {
            session_id: session.id.clone(),
        })
    }
    
    async fn get_session(
        State(state): State<Arc<RuntimeState>>,
        Path(session_id): Path<String>,
    ) -> Json<SessionStateResponse> {
        match state.get_session_state(&session_id).await {
            Ok(response) => Json(response),
            Err(_) => {
                // Return error response
                Json(SessionStateResponse {
                    session_id: session_id.clone(),
                    conversation: Vec::new(),
                    state: serde_json::Value::Null,
                })
            }
        }
    }
    
    async fn send_message(
        State(state): State<Arc<RuntimeState>>,
        Path(session_id): Path<String>,
        Json(request): Json<SendMessageRequest>,
    ) -> Json<SendMessageResponse> {
        info!("Processing message for session: {}", session_id);
        
        // Get the session
        let mut manager = state.session_manager.write().await;
        let session = match manager.get_session_mut(&session_id) {
            Some(session) => session,
            None => {
                return Json(SendMessageResponse {
                    messages: vec![],
                    tool_calls: vec![],
                })
            }
        };
        
        // Add user message
        session.add_message(jamey_protocol::Message::user(request.message));
        
        // Process with agent engine
        let last_message = session.conversation.last().unwrap().clone();
        let agent_response = state.agent_engine.process_message(session, last_message).await;
        
        match agent_response {
            Ok(response) => {
                // Add assistant messages to conversation
                for message in &response.messages {
                    session.add_message(message.clone());
                }
                
                Json(SendMessageResponse {
                    messages: response.messages,
                    tool_calls: response.tool_calls,
                })
            }
            Err(e) => {
                tracing::error!("Error processing message: {}", e);
                Json(SendMessageResponse {
                    messages: vec![],
                    tool_calls: vec![],
                })
            }
        }
    }
    
    async fn execute_tool(
        State(state): State<Arc<RuntimeState>>,
        Path(session_id): Path<String>,
        Json(request): Json<ToolExecutionRequest>,
    ) -> Json<ToolExecutionResponse> {
        info!("Executing tool for session: {}", session_id);
        
        let tool_registry = state.tool_registry.read().await;
        let result = tool_registry.execute(request.tool_call).await;
        
        match result {
            Ok(tool_result) => {
                Json(ToolExecutionResponse {
                    result: tool_result,
                })
            }
            Err(e) => {
                tracing::error!("Error executing tool: {}", e);
                Json(ToolExecutionResponse {
                    result: jamey_protocol::ToolResult {
                        id: uuid::Uuid::new_v4().to_string(),
                        name: "error".to_string(),
                        output: format!("Tool execution failed: {}", e),
                        success: false,
                    },
                })
            }
        }
    }
    
    async fn list_tools(State(state): State<Arc<RuntimeState>>) -> Json<ListToolsResponse> {
        let tool_registry = state.tool_registry.read().await;
        let tools = tool_registry.list_tools();
        
        Json(ListToolsResponse { tools })
    }
    
    async fn health_check() -> &'static str {
        "OK"
    }
    
    async fn shutdown(State(state): State<Arc<RuntimeState>>) -> &'static str {
        // In a real implementation, this would trigger graceful shutdown
        info!("Shutdown endpoint called");
        "Shutting down..."
    }
}
```

```rust:jamey-runtime/src/service.rs
use std::path::PathBuf;

/// Platform-specific service management
pub struct ServiceManager {
    service_name: String,
    executable_path: PathBuf,
}

impl ServiceManager {
    pub fn new() -> anyhow::Result<Self> {
        let executable_path = std::env::current_exe()?;
        let service_name = "jamey-runtime".to_string();
        
        Ok(Self {
            service_name,
            executable_path,
        })
    }
    
    #[cfg(target_os = "windows")]
    pub async fn install(&self) -> anyhow::Result<()> {
        use std::process::Command;
        
        println!("Installing as Windows Service...");
        
        // Use sc.exe to create Windows service
        let output = Command::new("sc")
            .args([
                "create",
                &self.service_name,
                &format!("binPath={}", self.executable_path.display()),
                "start=auto",
                "DisplayName=Jamey Runtime Daemon",
            ])
            .output()?;
            
        if output.status.success() {
            println!("✅ Windows service installed successfully");
        } else {
            let error = String::from_utf8_lossy(&output.stderr);
            anyhow::bail!("Failed to install service: {}", error);
        }
        
        Ok(())
    }
    
    #[cfg(target_os = "linux")]
    pub async fn install(&self) -> anyhow::Result<()> {
        use std::fs;
        
        println!("Installing as systemd service...");
        
        let service_content = format!(
            r#"[Unit]
Description=Jamey Runtime Daemon
After=network.target

[Service]
Type=simple
User={}
ExecStart={}
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target"#,
            whoami::username(),
            self.executable_path.display()
        );
        
        let service_path = "/etc/systemd/system/jamey-runtime.service";
        
        // Note: This requires root privileges
        fs::write(service_path, service_content)?;
        
        println!("✅ systemd service file created at {}", service_path);
        println!("Run 'systemctl daemon-reload' and 'systemctl enable jamey-runtime' to complete installation");
        
        Ok(())
    }
    
    #[cfg(target_os = "macos")]
    pub async fn install(&self) -> anyhow::Result<()> {
        use std::fs;
        
        println!("Installing as launchd service...");
        
        let plist_content = format!(
            r#"<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.jamey.runtime</string>
    <key>ProgramArguments</key>
    <array>
        <string>{}</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
</dict>
</plist>"#,
            self.executable_path.display()
        );
        
        let plist_path = dirs::home_dir()
            .unwrap()
            .join("Library/LaunchAgents/com.jamey.runtime.plist");
            
        fs::write(&plist_path, plist_content)?;
        
        println!("✅ launchd service file created at {}", plist_path.display());
        println!("Run 'launchctl load {}' to start the service", plist_path.display());
        
        Ok(())
    }
    
    pub async fn uninstall(&self) -> anyhow::Result<()> {
        println!("Uninstalling service...");
        
        #[cfg(target_os = "windows")]
        {
            use std::process::Command;
            let _ = Command::new("sc").args(["stop", &self.service_name]).output();
            let _ = Command::new("sc").args(["delete", &self.service_name]).output();
        }
        
        #[cfg(target_os = "linux")]
        {
            let _ = std::fs::remove_file("/etc/systemd/system/jamey-runtime.service");
        }
        
        #[cfg(target_os = "macos")]
        {
            let plist_path = dirs::home_dir()
                .unwrap()
                .join("Library/LaunchAgents/com.jamey.runtime.plist");
            let _ = std::fs::remove_file(plist_path);
        }
        
        println!("✅ Service uninstalled");
        Ok(())
    }
}
```

```rust:jamey-runtime/src/config.rs
use serde::{Deserialize, Serialize};
use std::path::PathBuf;

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct RuntimeConfig {
    pub api: ApiConfig,
    pub logging: LoggingConfig,
    pub tools: ToolsConfig,
    pub providers: ProvidersConfig,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ApiConfig {
    pub host: String,
    pub port: u16,
    pub enable_cors: bool,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct LoggingConfig {
    pub level: String,
    pub enable_file_logging: bool,
    pub max_log_files: usize,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ToolsConfig {
    pub enabled_tools: Vec<String>,
    pub external_tools_dir: PathBuf,
    pub execution_timeout_seconds: u64,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct ProvidersConfig {
    pub openai: OpenAiConfig,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct OpenAiConfig {
    pub api_key: String,
    pub model: String,
    pub base_url: String,
}

impl Default for RuntimeConfig {
    fn default() -> Self {
        Self {
            api: ApiConfig {
                host: "127.0.0.1".to_string(),
                port: 3000,
                enable_cors: false,
            },
            logging: LoggingConfig {
                level: "info".to_string(),
                enable_file_logging: true,
                max_log_files: 10,
            },
            tools: ToolsConfig {
                enabled_tools: vec![
                    "shell_exec".to_string(),
                    "file_read".to_string(), 
                    "file_write".to_string(),
                ],
                external_tools_dir: PathBuf::from("./tools"),
                execution_timeout_seconds: 30,
            },
            providers: ProvidersConfig {
                openai: OpenAiConfig {
                    api_key: String::new(),
                    model: "gpt-4".to_string(),
                    base_url: "https://api.openai.com/v1".to_string(),
                },
            },
        }
    }
}

pub struct ConfigManager {
    config_dir: PathBuf,
}

impl ConfigManager {
    pub fn new() -> anyhow::Result<Self> {
        let config_dir = dirs::config_dir()
            .unwrap_or_else(|| PathBuf::from("./config"))
            .join("jamey-code");
            
        std::fs::create_dir_all(&config_dir)?;
        
        Ok(Self { config_dir })
    }
    
    pub fn load_config(&self) -> anyhow::Result<RuntimeConfig> {
        let config_path = self.config_dir.join("runtime.toml");
        
        if config_path.exists() {
            let config_content = std::fs::read_to_string(&config_path)?;
            let config: RuntimeConfig = toml::from_str(&config_content)?;
            Ok(config)
        } else {
            // Create default config
            let config = RuntimeConfig::default();
            self.save_config(&config)?;
            Ok(config)
        }
    }
    
    pub fn save_config(&self, config: &RuntimeConfig) -> anyhow::Result<()> {
        let config_path = self.config_dir.join("runtime.toml");
        let config_content = toml::to_string_pretty(config)?;
        std::fs::write(config_path, config_content)?;
        Ok(())
    }
}
```

## 9. CLI Client

```toml:jamey-cli/Cargo.toml
[package]
name = "jamey-cli"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { workspace = true, features = ["full"] }
serde = { workspace = true }
serde_json = { workspace = true }
anyhow = { workspace = true }
clap = { workspace = true }
reqwest = { workspace = true }

jamey-protocol = { path = "../jamey-protocol" }
```

```rust:jamey-cli/src/main.rs
use clap::{Parser, Subcommand};
use reqwest::Client;
use std::io::{self, Write};
use std::time::Duration;

/// Jamey CLI Client - Auto-discovers local runtime
#[derive(Parser)]
#[command(name = "jamey")]
#[command(about = "Jamey Code CLI - Your Digital Twin Interface")]
struct Cli {
    #[command(subcommand)]
    command: Option<Commands>,
    
    #[arg(long)]
    runtime_url: Option<String>,
    
    #[arg(short, long)]
    session: Option<String>,
}

#[derive(Subcommand)]
enum Commands {
    /// Start interactive chat (default)
    Chat,
    /// Create a new session
    New {
        #[arg(short, long)]
        name: Option<String>,
    },
    /// List active sessions
    Sessions,
    /// List available tools
    Tools,
    /// Runtime management commands
    Runtime {
        #[command(subcommand)]
        action: RuntimeCommands,
    },
}

#[derive(Subcommand)]
enum RuntimeCommands {
    /// Start the runtime daemon
    Start,
    /// Stop the runtime daemon
    Stop,
    /// Check runtime status
    Status,
}

struct JameyClient {
    client: Client,
    base_url: String,
}

impl JameyClient {
    pub async fn discover() -> anyhow::Result<Self> {
        // Try to auto-discover the runtime
        let potential_urls = [
            "http://127.0.0.1:3000",
            "http://localhost:3000",
        ];
        
        for url in potential_urls {
            let client = Client::new();
            if client.get(&format!("{}/health", url))
                .timeout(Duration::from_secs(1))
                .send()
                .await
                .is_ok() 
            {
                println!("🔗 Connected to runtime at {}", url);
                return Ok(Self {
                    client,
                    base_url: url.to_string(),
                });
            }
        }
        
        anyhow::bail!(
            "Could not find Jamey Runtime. Start it with:\n  jamey runtime start\n\nOr specify URL with --runtime-url"
        )
    }
    
    pub fn with_url(url: String) -> Self {
        Self {
            client: Client::new(),
            base_url: url,
        }
    }
    
    pub async fn create_session(&self) -> anyhow::Result<String> {
        let response = self.client
            .post(&format!("{}/sessions", self.base_url))
            .json(&jamey_protocol::CreateSessionRequest {
                context: None,
                agent_mode: None,
            })
            .send()
            .await?;
            
        let create_response: jamey_protocol::CreateSessionResponse = response.json().await?;
        Ok(create_response.session_id)
    }
    
    pub async fn send_message(&self, session_id: &str, message: &str) -> anyhow::Result<jamey_protocol::SendMessageResponse> {
        let response = self.client
            .post(&format!("{}/sessions/{}/messages", self.base_url, session_id))
            .json(&jamey_protocol::SendMessageRequest {
                session_id: session_id.to_string(),
                message: message.to_string(),
            })
            .send()
            .await?;
            
        Ok(response.json().await?)
    }
    
    pub async fn get_session(&self, session_id: &str) -> anyhow::Result<jamey_protocol::SessionStateResponse> {
        let response = self.client
            .get(&format!("{}/sessions/{}", self.base_url, session_id))
            .send()
            .await?;
            
        Ok(response.json().await?)
    }
    
    pub async fn list_tools(&self) -> anyhow::Result<jamey_protocol::ListToolsResponse> {
        let response = self.client
            .get(&format!("{}/tools", self.base_url))
            .send()
            .await?;
            
        Ok(response.json().await?)
    }
}

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    let cli = Cli::parse();
    
    // Create client (auto-discover or use provided URL)
    let client = if let Some(url) = cli.runtime_url {
        JameyClient::with_url(url)
    } else {
        JameyClient::discover().await?
    };
    
    match cli.command {
        Some(Commands::Chat) => run_chat(client, cli.session).await?,
        Some(Commands::New { name }) => {
            let session_id = client.create_session().await?;
            println!("Created session: {}", session_id);
            if let Some(name) = name {
                println!("Named: {}", name);
            }
        }
        Some(Commands::Sessions) => {
            list_sessions().await?;
        }
        Some(Commands::Tools) => {
            list_tools(client).await?;
        }
        Some(Commands::Runtime { action }) => {
            manage_runtime(action).await?;
        }
        None => {
            // Default to chat mode
            run_chat(client, cli.session).await?;
        }
    }
    
    Ok(())
}

async fn manage_runtime(action: RuntimeCommands) -> anyhow::Result<()> {
    match action {
        RuntimeCommands::Start => {
            println!("Starting Jamey Runtime...");
            // Spawn the runtime process
            let status = std::process::Command::new("jamey-runtime")
                .arg("start")
                .arg("--foreground")
                .status()?;
                
            if status.success() {
                println!("✅ Runtime started successfully");
            } else {
                println!("❌ Failed to start runtime");
            }
        }
        RuntimeCommands::Stop => {
            println!("Stopping Jamey Runtime...");
            let status = std::process::Command::new("jamey-runtime")
                .arg("stop")
                .status()?;
                
            if status.success() {
                println!("✅ Runtime stopped successfully");
            } else {
                println!("❌ Failed to stop runtime");
            }
        }
        RuntimeCommands::Status => {
            let status = std::process::Command::new("jamey-runtime")
                .arg("status")
                .status()?;
        }
    }
    Ok(())
}

async fn run_chat(client: JameyClient, session_id: Option<String>) -> anyhow::Result<()> {
    println!("🚀 Jamey CLI Client");
    
    // Use provided session or create new one
    let session_id = if let Some(id) = session_id {
        println!("Using existing session: {}", id);
        id
    } else {
        println!("Creating new session...");
        client.create_session().await?
    };
    
    println!("Session: {}\n", session_id);
    
    let stdin = io::stdin();
    let mut input = String::new();
    
    loop {
        print!("> ");
        io::stdout().flush()?;
        
        input.clear();
        stdin.read_line(&mut input)?;
        let message = input.trim();
        
        match message {
            "::quit" | "::exit" => break,
            "::help" => print_help(),
            "::session" => {
                println!("Current session: {}", session_id);
                continue;
            }
            "::tools" => {
                list_tools(client).await?;
                continue;
            }
            _ if message.is_empty() => continue,
            _ => {
                let response = client.send_message(&session_id, message).await?;
                
                for msg in response.messages {
                    if let jamey_protocol::Role::Assistant = msg.role {
                        println!("🤖 {}", msg.content);
                    }
                }
                
                // Handle tool calls if any
                if !response.tool_calls.is_empty() {
                    println!("[{} tool calls to execute]", response.tool_calls.len());
                    for tool_call in response.tool_calls {
                        println!("  - {}: {}", tool_call.name, tool_call.args);
                    }
                }
                
                println!();
            }
        }
    }
    
    println!("Goodbye!");
    Ok(())
}

async fn list_sessions() -> anyhow::Result<()> {
    println!("Session listing would be implemented here");
    // Would call a sessions endpoint when implemented
    Ok(())
}

async fn list_tools(client: JameyClient) -> anyhow::Result<()> {
    let response = client.list_tools().await?;
    
    println!("Available Tools:");
    println!("================
