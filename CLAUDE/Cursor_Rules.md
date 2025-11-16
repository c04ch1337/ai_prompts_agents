# Digital Twin Agentic AI Desktop - Complete Project Plan

## Project Overview
A high-performance, monolithic agentic AI desktop application built entirely in Rust, designed for digital twin simulation, monitoring, and intelligent interaction. Inspired by Claude Desktop's architecture but optimized for digital twin use cases.

---

## 1. Technology Stack

### Core Technologies
- **Language**: Rust (stable, latest)
- **Desktop Framework**: Tauri 2.0+ (WebView-based UI)
- **Frontend**: React 18+ with TypeScript
- **State Management**: Zustand + React Query
- **Styling**: Tailwind CSS
- **Database**: SQLite (via rusqlite)
- **Async Runtime**: Tokio
- **Serialization**: Serde

### Key Crates
```toml
[dependencies]
# Core Framework
tauri = { version = "2.0", features = ["shell-open"] }
tokio = { version = "1.35", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"

# Database & Persistence
rusqlite = { version = "0.30", features = ["bundled"] }
sqlx = { version = "0.7", features = ["sqlite", "runtime-tokio-rustls"] }

# HTTP & Networking
reqwest = { version = "0.11", features = ["json", "stream"] }
tokio-tungstenite = "0.21" # WebSocket support

# LLM Integration
async-openai = "0.18" # OpenAI/compatible APIs
anthropic-sdk = "0.1" # Claude API

# Tools & Utilities
uuid = { version = "1.6", features = ["v4", "serde"] }
chrono = { version = "0.4", features = ["serde"] }
thiserror = "1.0"
anyhow = "1.0"
tracing = "0.1"
tracing-subscriber = "0.3"

# Security
ring = "0.17" # Cryptography
base64 = "0.21"

# Digital Twin Specific
modbus = "1.0" # Industrial protocols
mqtt = "0.11" # IoT messaging
```

---

## 2. Project Structure

```
digital-twin-desktop/
├── .env.local.example          # Environment configuration template
├── .gitignore
├── Cargo.toml                  # Rust dependencies
├── package.json                # Frontend dependencies
├── tauri.conf.json             # Tauri configuration
├── build.rs                    # Build script
│
├── .cursorrules                # Cursor IDE rules
├── .cursorprompts/             # Cursor prompt templates
│   ├── architecture.md
│   ├── agent-patterns.md
│   └── testing-guidelines.md
│
├── src-tauri/                  # Rust backend
│   ├── Cargo.toml
│   ├── build.rs
│   ├── tauri.conf.json
│   │
│   ├── src/
│   │   ├── main.rs             # Application entry point
│   │   │
│   │   ├── core/               # Domain Logic (Inner Layer)
│   │   │   ├── mod.rs
│   │   │   ├── agent/
│   │   │   │   ├── mod.rs
│   │   │   │   ├── orchestrator.rs      # Main agent loop
│   │   │   │   ├── state_machine.rs     # Agent state management
│   │   │   │   └── context_manager.rs   # Prompt context building
│   │   │   │
│   │   │   ├── reasoning/
│   │   │   │   ├── mod.rs
│   │   │   │   ├── planner.rs           # Task planning logic
│   │   │   │   ├── executor.rs          # Plan execution
│   │   │   │   └── reflector.rs         # Self-reflection & learning
│   │   │   │
│   │   │   ├── tools/          # Tool abstractions (interfaces only)
│   │   │   │   ├── mod.rs
│   │   │   │   ├── registry.rs          # Tool discovery & registration
│   │   │   │   ├── tool_trait.rs        # Common tool interface
│   │   │   │   └── safety.rs            # Sandboxing & permissions
│   │   │   │
│   │   │   ├── models/         # Domain models
│   │   │   │   ├── mod.rs
│   │   │   │   ├── conversation.rs
│   │   │   │   ├── message.rs
│   │   │   │   ├── digital_twin.rs      # Twin representation
│   │   │   │   ├── sensor_data.rs       # IoT sensor models
│   │   │   │   └── simulation.rs        # Simulation state
│   │   │   │
│   │   │   └── errors.rs       # Domain-specific errors
│   │   │
│   │   ├── application/        # Use Cases (Application Layer)
│   │   │   ├── mod.rs
│   │   │   ├── use_cases/
│   │   │   │   ├── mod.rs
│   │   │   │   ├── send_message.rs
│   │   │   │   ├── process_file.rs
│   │   │   │   ├── execute_tool.rs
│   │   │   │   ├── sync_digital_twin.rs # Twin synchronization
│   │   │   │   ├── run_simulation.rs    # Simulation execution
│   │   │   │   └── analyze_sensor_data.rs
│   │   │   │
│   │   │   └── services/
│   │   │       ├── mod.rs
│   │   │       ├── conversation_service.rs
│   │   │       ├── twin_service.rs
│   │   │       └── analytics_service.rs
│   │   │
│   │   ├── infrastructure/     # External Integrations (Outer Layer)
│   │   │   ├── mod.rs
│   │   │   │
│   │   │   ├── llm/            # LLM provider implementations
│   │   │   │   ├── mod.rs
│   │   │   │   ├── client_trait.rs
│   │   │   │   ├── anthropic_client.rs
│   │   │   │   ├── openai_client.rs
│   │   │   │   └── streaming.rs
│   │   │   │
│   │   │   ├── tools/          # Concrete tool implementations
│   │   │   │   ├── mod.rs
│   │   │   │   ├── web_search.rs
│   │   │   │   ├── calculator.rs
│   │   │   │   ├── file_operations.rs
│   │   │   │   ├── code_executor.rs
│   │   │   │   ├── modbus_client.rs     # Industrial protocol tool
│   │   │   │   ├── mqtt_client.rs       # IoT messaging tool
│   │   │   │   └── twin_query.rs        # Digital twin queries
│   │   │   │
│   │   │   ├── persistence/    # Data storage
│   │   │   │   ├── mod.rs
│   │   │   │   ├── database.rs          # SQLite connection pool
│   │   │   │   ├── repositories/
│   │   │   │   │   ├── mod.rs
│   │   │   │   │   ├── conversation_repo.rs
│   │   │   │   │   ├── settings_repo.rs
│   │   │   │   │   ├── twin_repo.rs
│   │   │   │   │   └── sensor_repo.rs
│   │   │   │   └── migrations/
│   │   │   │       ├── mod.rs
│   │   │   │       └── m001_initial.sql
│   │   │   │
│   │   │   └── system/         # System integrations
│   │   │       ├── mod.rs
│   │   │       ├── filesystem.rs
│   │   │       ├── clipboard.rs
│   │   │       └── notifications.rs
│   │   │
│   │   ├── api/                # Tauri Commands (IPC Layer)
│   │   │   ├── mod.rs
│   │   │   ├── commands/
│   │   │   │   ├── mod.rs
│   │   │   │   ├── conversation.rs
│   │   │   │   ├── agent.rs
│   │   │   │   ├── tools.rs
│   │   │   │   ├── settings.rs
│   │   │   │   ├── twin.rs
│   │   │   │   └── simulation.rs
│   │   │   │
│   │   │   └── handlers/
│   │   │       ├── mod.rs
│   │   │       ├── error_handler.rs
│   │   │       └── streaming_handler.rs
│   │   │
│   │   ├── config/             # Configuration management
│   │   │   ├── mod.rs
│   │   │   ├── app_config.rs
│   │   │   ├── llm_config.rs
│   │   │   └── twin_config.rs
│   │   │
│   │   └── utils/              # Shared utilities
│   │       ├── mod.rs
│   │       ├── crypto.rs       # Encryption utilities
│   │       ├── token_counter.rs
│   │       └── logger.rs
│   │
│   └── tests/
│       ├── unit/
│       │   ├── core/
│       │   └── application/
│       ├── integration/
│       │   ├── tools/
│       │   └── llm/
│       └── e2e/
│
├── src/                        # React Frontend
│   ├── main.tsx
│   ├── App.tsx
│   │
│   ├── components/
│   │   ├── chat/
│   │   │   ├── ChatWindow.tsx
│   │   │   ├── MessageList.tsx
│   │   │   ├── MessageInput.tsx
│   │   │   └── StreamingMessage.tsx
│   │   │
│   │   ├── agent/
│   │   │   ├── AgentStatus.tsx
│   │   │   ├── ToolExecutionViewer.tsx
│   │   │   └── ThinkingIndicator.tsx
│   │   │
│   │   ├── twin/
│   │   │   ├── TwinDashboard.tsx
│   │   │   ├── SensorGrid.tsx
│   │   │   ├── SimulationControls.tsx
│   │   │   └── TwinVisualizer.tsx
│   │   │
│   │   └── common/
│   │       ├── LoadingSpinner.tsx
│   │       └── ErrorBoundary.tsx
│   │
│   ├── stores/                 # Zustand state management
│   │   ├── chatStore.ts
│   │   ├── agentStore.ts
│   │   ├── twinStore.ts
│   │   └── settingsStore.ts
│   │
│   ├── hooks/
│   │   ├── useChat.ts
│   │   ├── useAgent.ts
│   │   ├── useTwin.ts
│   │   └── useStreaming.ts
│   │
│   ├── api/                    # Tauri API wrappers
│   │   ├── tauri.ts
│   │   ├── chat.ts
│   │   ├── agent.ts
│   │   └── twin.ts
│   │
│   ├── types/
│   │   ├── chat.ts
│   │   ├── agent.ts
│   │   └── twin.ts
│   │
│   └── styles/
│       └── globals.css
│
├── docs/                       # Documentation
│   ├── architecture.md
│   ├── agent-design.md
│   ├── tool-development.md
│   ├── digital-twin-guide.md
│   └── deployment.md
│
└── scripts/                    # Build & deployment scripts
    ├── setup.sh
    ├── dev.sh
    └── build.sh
```

---

## 3. Configuration Files

### 3.1 .env.local.example

```bash
# =============================================================================
# DIGITAL TWIN AGENTIC AI DESKTOP - ENVIRONMENT CONFIGURATION
# =============================================================================
# Copy this file to .env.local and fill in your actual values
# NEVER commit .env.local to version control
# =============================================================================

# -----------------------------------------------------------------------------
# APPLICATION SETTINGS
# -----------------------------------------------------------------------------
# Application name displayed in UI
APP_NAME="Digital Twin AI Desktop"

# Application version (semantic versioning)
APP_VERSION="0.1.0"

# Environment: development, staging, production
ENVIRONMENT="development"

# Log level: trace, debug, info, warn, error
LOG_LEVEL="debug"

# Enable debug mode (verbose logging, dev tools)
DEBUG_MODE="true"

# Application data directory (leave empty for OS default)
# Windows: %APPDATA%/digital-twin-ai
# macOS: ~/Library/Application Support/digital-twin-ai
# Linux: ~/.local/share/digital-twin-ai
APP_DATA_DIR=""

# -----------------------------------------------------------------------------
# DATABASE SETTINGS
# -----------------------------------------------------------------------------
# SQLite database file name
DATABASE_NAME="digital_twin.db"

# Database connection pool size
DATABASE_POOL_SIZE="10"

# Enable SQLite Write-Ahead Logging (recommended for performance)
DATABASE_WAL_MODE="true"

# Auto-vacuum mode: none, full, incremental
DATABASE_AUTO_VACUUM="incremental"

# Enable foreign key constraints
DATABASE_FOREIGN_KEYS="true"

# -----------------------------------------------------------------------------
# LLM PROVIDER SETTINGS - ANTHROPIC (CLAUDE)
# -----------------------------------------------------------------------------
# Anthropic API key (get from: https://console.anthropic.com/)
ANTHROPIC_API_KEY="sk-ant-api03-..."

# Claude model to use: claude-3-opus-20240229, claude-3-sonnet-20240229, claude-3-haiku-20240307
ANTHROPIC_MODEL="claude-3-sonnet-20240229"

# API base URL (change for proxy or custom endpoint)
ANTHROPIC_API_BASE="https://api.anthropic.com"

# API version
ANTHROPIC_API_VERSION="2023-06-01"

# Max tokens for Claude responses
ANTHROPIC_MAX_TOKENS="4096"

# Temperature (0.0-1.0, lower = more deterministic)
ANTHROPIC_TEMPERATURE="0.7"

# Top-p sampling (0.0-1.0)
ANTHROPIC_TOP_P="1.0"

# Request timeout in seconds
ANTHROPIC_TIMEOUT="120"

# Max retries for failed requests
ANTHROPIC_MAX_RETRIES="3"

# Enable streaming responses
ANTHROPIC_ENABLE_STREAMING="true"

# -----------------------------------------------------------------------------
# LLM PROVIDER SETTINGS - OPENAI (GPT)
# -----------------------------------------------------------------------------
# OpenAI API key (get from: https://platform.openai.com/)
OPENAI_API_KEY="sk-..."

# GPT model: gpt-4-turbo-preview, gpt-4, gpt-3.5-turbo
OPENAI_MODEL="gpt-4-turbo-preview"

# API base URL
OPENAI_API_BASE="https://api.openai.com/v1"

# Organization ID (optional)
OPENAI_ORGANIZATION=""

# Max tokens
OPENAI_MAX_TOKENS="4096"

# Temperature
OPENAI_TEMPERATURE="0.7"

# Request timeout in seconds
OPENAI_TIMEOUT="120"

# Max retries
OPENAI_MAX_RETRIES="3"

# Enable streaming
OPENAI_ENABLE_STREAMING="true"

# -----------------------------------------------------------------------------
# AGENT CONFIGURATION
# -----------------------------------------------------------------------------
# Default LLM provider: anthropic, openai
DEFAULT_LLM_PROVIDER="anthropic"

# Agent system prompt file (relative to config directory)
AGENT_SYSTEM_PROMPT_FILE="system_prompts/default.txt"

# Max conversation history messages to keep in context
AGENT_MAX_HISTORY_MESSAGES="50"

# Max total tokens in context window
AGENT_MAX_CONTEXT_TOKENS="100000"

# Enable agent reasoning transparency (show thinking process)
AGENT_SHOW_REASONING="true"

# Max tool execution time in seconds
AGENT_TOOL_TIMEOUT="30"

# Max parallel tool executions
AGENT_MAX_PARALLEL_TOOLS="3"

# Enable automatic tool retry on failure
AGENT_AUTO_RETRY_TOOLS="true"

# Max tool retry attempts
AGENT_MAX_TOOL_RETRIES="2"

# Agent planning strategy: simple, chain_of_thought, tree_of_thought
AGENT_PLANNING_STRATEGY="chain_of_thought"

# Enable agent self-reflection
AGENT_ENABLE_REFLECTION="true"

# -----------------------------------------------------------------------------
# DIGITAL TWIN SETTINGS
# -----------------------------------------------------------------------------
# Enable digital twin features
TWIN_ENABLED="true"

# Twin synchronization interval in seconds
TWIN_SYNC_INTERVAL="5"

# Enable real-time twin updates
TWIN_REALTIME_UPDATES="true"

# Twin state persistence: memory, disk, database
TWIN_PERSISTENCE="database"

# Enable twin simulation mode
TWIN_SIMULATION_ENABLED="true"

# Default simulation timestep in milliseconds
TWIN_SIMULATION_TIMESTEP="100"

# Max twin entities
TWIN_MAX_ENTITIES="1000"

# -----------------------------------------------------------------------------
# SENSOR & IOT SETTINGS
# -----------------------------------------------------------------------------
# Enable sensor data collection
SENSORS_ENABLED="true"

# Sensor data retention period in days
SENSORS_RETENTION_DAYS="90"

# Sensor sampling rate in milliseconds
SENSORS_SAMPLING_RATE="1000"

# Enable sensor data aggregation
SENSORS_ENABLE_AGGREGATION="true"

# Aggregation window in seconds
SENSORS_AGGREGATION_WINDOW="60"

# -----------------------------------------------------------------------------
# MODBUS CONFIGURATION (Industrial Protocol)
# -----------------------------------------------------------------------------
# Enable Modbus support
MODBUS_ENABLED="false"

# Modbus slave address
MODBUS_SLAVE_ADDRESS="1"

# Modbus connection timeout in milliseconds
MODBUS_TIMEOUT="1000"

# Modbus TCP host
MODBUS_TCP_HOST="127.0.0.1"

# Modbus TCP port
MODBUS_TCP_PORT="502"

# Modbus RTU serial port
MODBUS_RTU_PORT="/dev/ttyUSB0"

# Modbus RTU baud rate
MODBUS_RTU_BAUD_RATE="9600"

# -----------------------------------------------------------------------------
# MQTT CONFIGURATION (IoT Messaging)
# -----------------------------------------------------------------------------
# Enable MQTT support
MQTT_ENABLED="false"

# MQTT broker host
MQTT_BROKER_HOST="localhost"

# MQTT broker port
MQTT_BROKER_PORT="1883"

# MQTT client ID
MQTT_CLIENT_ID="digital_twin_desktop"

# MQTT username (leave empty for anonymous)
MQTT_USERNAME=""

# MQTT password
MQTT_PASSWORD=""

# MQTT keep-alive interval in seconds
MQTT_KEEP_ALIVE="60"

# MQTT QoS level: 0, 1, 2
MQTT_QOS="1"

# MQTT topic prefix
MQTT_TOPIC_PREFIX="digital_twin"

# Enable MQTT TLS
MQTT_TLS="false"

# -----------------------------------------------------------------------------
# TOOL SETTINGS
# -----------------------------------------------------------------------------
# Enable web search tool
TOOL_WEB_SEARCH_ENABLED="true"

# Web search API provider: brave, google, bing
TOOL_WEB_SEARCH_PROVIDER="brave"

# Web search API key
TOOL_WEB_SEARCH_API_KEY=""

# Max web search results
TOOL_WEB_SEARCH_MAX_RESULTS="5"

# Enable calculator tool
TOOL_CALCULATOR_ENABLED="true"

# Enable file operations tool
TOOL_FILE_OPS_ENABLED="true"

# File operations allowed directories (comma-separated)
TOOL_FILE_OPS_ALLOWED_DIRS="~/Documents,~/Downloads"

# Enable code execution tool
TOOL_CODE_EXEC_ENABLED="false"

# Code execution sandbox: docker, vm, none (DANGEROUS)
TOOL_CODE_EXEC_SANDBOX="docker"

# Code execution timeout in seconds
TOOL_CODE_EXEC_TIMEOUT="30"

# Enable screenshot tool
TOOL_SCREENSHOT_ENABLED="true"

# Enable clipboard tool
TOOL_CLIPBOARD_ENABLED="true"

# -----------------------------------------------------------------------------
# SECURITY SETTINGS
# -----------------------------------------------------------------------------
# Enable API key encryption at rest
SECURITY_ENCRYPT_API_KEYS="true"

# Master encryption key (generate with: openssl rand -hex 32)
# IMPORTANT: Keep this secret and back it up securely
SECURITY_MASTER_KEY=""

# Enable conversation encryption
SECURITY_ENCRYPT_CONVERSATIONS="false"

# Enable audit logging
SECURITY_ENABLE_AUDIT_LOG="true"

# Require user confirmation for dangerous tool operations
SECURITY_REQUIRE_CONFIRMATION="true"

# Whitelist allowed domains for web fetch (comma-separated, * for all)
SECURITY_ALLOWED_DOMAINS="*"

# Enable Content Security Policy
SECURITY_CSP_ENABLED="true"

# -----------------------------------------------------------------------------
# PERFORMANCE SETTINGS
# -----------------------------------------------------------------------------
# Enable response caching
CACHE_ENABLED="true"

# Cache TTL in seconds
CACHE_TTL="3600"

# Max cache size in MB
CACHE_MAX_SIZE_MB="100"

# Enable request rate limiting
RATE_LIMIT_ENABLED="true"

# Max requests per minute
RATE_LIMIT_MAX_REQUESTS="60"

# Enable background task queue
TASK_QUEUE_ENABLED="true"

# Max concurrent background tasks
TASK_QUEUE_MAX_CONCURRENT="5"

# -----------------------------------------------------------------------------
# UI/UX SETTINGS
# -----------------------------------------------------------------------------
# Default UI theme: light, dark, auto
UI_THEME="auto"

# Enable syntax highlighting in code blocks
UI_SYNTAX_HIGHLIGHTING="true"

# Enable markdown rendering
UI_MARKDOWN_ENABLED="true"

# Enable LaTeX math rendering
UI_LATEX_ENABLED="true"

# Font family for chat
UI_FONT_FAMILY="Inter, system-ui, sans-serif"

# Font size in pixels
UI_FONT_SIZE="14"

# Enable animations
UI_ANIMATIONS_ENABLED="true"

# Enable sound effects
UI_SOUNDS_ENABLED="false"

# Auto-scroll to new messages
UI_AUTO_SCROLL="true"

# Show timestamps on messages
UI_SHOW_TIMESTAMPS="true"

# Message timestamp format: relative, absolute, both
UI_TIMESTAMP_FORMAT="relative"

# -----------------------------------------------------------------------------
# TELEMETRY & ANALYTICS
# -----------------------------------------------------------------------------
# Enable anonymous usage telemetry
TELEMETRY_ENABLED="false"

# Telemetry endpoint
TELEMETRY_ENDPOINT=""

# Enable error reporting
ERROR_REPORTING_ENABLED="false"

# Error reporting endpoint (e.g., Sentry DSN)
ERROR_REPORTING_ENDPOINT=""

# Enable performance monitoring
PERFORMANCE_MONITORING_ENABLED="false"

# -----------------------------------------------------------------------------
# DEVELOPMENT SETTINGS
# -----------------------------------------------------------------------------
# Enable hot reload for frontend
DEV_HOT_RELOAD="true"

# Enable Rust backtrace
RUST_BACKTRACE="1"

# Enable Tauri devtools
DEV_TOOLS_ENABLED="true"

# Mock LLM responses (for testing without API calls)
DEV_MOCK_LLM="false"

# Mock twin data (for testing without real sensors)
DEV_MOCK_TWIN_DATA="false"

# Seed database with test data
DEV_SEED_DATABASE="false"

# -----------------------------------------------------------------------------
# BACKUP & RECOVERY
# -----------------------------------------------------------------------------
# Enable automatic backups
BACKUP_ENABLED="true"

# Backup interval in hours
BACKUP_INTERVAL_HOURS="24"

# Max backup files to keep
BACKUP_MAX_FILES="7"

# Backup directory (relative to app data directory)
BACKUP_DIRECTORY="backups"

# Enable cloud backup
BACKUP_CLOUD_ENABLED="false"

# Cloud backup provider: s3, gcs, azure
BACKUP_CLOUD_PROVIDER=""

# Cloud backup credentials (provider-specific)
BACKUP_CLOUD_CREDENTIALS=""

# -----------------------------------------------------------------------------
# EXPERIMENTAL FEATURES
# -----------------------------------------------------------------------------
# Enable multimodal input (images, audio)
EXPERIMENTAL_MULTIMODAL="false"

# Enable voice input
EXPERIMENTAL_VOICE_INPUT="false"

# Enable voice output (TTS)
EXPERIMENTAL_VOICE_OUTPUT="false"

# Enable collaborative features
EXPERIMENTAL_COLLABORATION="false"

# Enable plugin system
EXPERIMENTAL_PLUGINS="false"

# Plugin directory
EXPERIMENTAL_PLUGIN_DIR="plugins"

# =============================================================================
# END OF CONFIGURATION
# =============================================================================
```

### 3.2 .cursorrules

```markdown
# Digital Twin Agentic AI Desktop - Cursor IDE Rules

## Project Context
This is a Rust-based desktop application using Tauri for cross-platform support. The application implements a sophisticated agentic AI system for digital twin simulation and monitoring.

## Architecture Principles

### 1. Clean Architecture (Onion/Hexagonal)
- **Core Domain** (innermost): Pure business logic, no external dependencies
- **Application Layer**: Use cases that orchestrate domain logic
- **Infrastructure Layer**: External integrations (LLM APIs, database, file system)
- **API Layer**: Tauri commands for frontend-backend communication

### 2. Dependency Rules
- Inner layers NEVER depend on outer layers
- Outer layers depend on inner layers through traits/interfaces
- Use dependency injection via trait objects or generics

### 3. Code Organization
```
core/       → Pure domain logic, zero external dependencies
application/ → Use cases, depends only on core
infrastructure/ → External integrations, implements core interfaces
api/        → Tauri commands, depends on application
```

## Rust Best Practices

### Error Handling
- Use `thiserror` for domain errors
- Use `anyhow` for application/infrastructure errors
- Never use `.unwrap()` in production code
- Always propagate errors with `?` operator
- Create domain-specific error types

```rust
// Good
#[derive(Debug, thiserror::Error)]
pub enum AgentError {
    #[error("Tool not found: {0}")]
    ToolNotFound(String),
    
    #[error("LLM request failed: {0}")]
    LlmRequestFailed(#[from] reqwest::Error),
}

// Usage
pub async fn execute_tool(&self, name: &str) -> Result<String, AgentError> {
    let tool = self.registry.get(name)
        .ok_or_else(|| AgentError::ToolNotFound(name.to_string()))?;
    Ok(tool.execute().await?)
}
```

### Async/Await
- Use `tokio` for async runtime
- Prefer `async fn` over manual `Future` implementations
- Use `#[tokio::test]` for async tests
- Handle cancellation gracefully

### Type Safety
- Use strong typing over stringly-typed code
- Leverage newtype pattern for domain concepts
- Use enums for state machines
- Prefer `Option<T>` over nullable types

```rust
// Good: Strong typing
pub struct ConversationId(Uuid);
pub struct MessageId(Uuid);

pub enum AgentState {
    Idle,
    Thinking { context: Context },
    ExecutingTool { tool: String, input: String },
    AwaitingUserInput,
    Error { error: AgentError },
}
```

### Traits and Generics
- Define traits in core, implement in infrastructure
- Use trait bounds to constrain generics
- Prefer static dispatch (`impl Trait`) over dynamic dispatch (`Box<dyn Trait>`)

```rust
// core/tools/tool_trait.rs
#[async_trait]
pub trait Tool: Send + Sync {
    fn name(&self) -> &str;
    fn description(&self) -> &str;
    async fn execute(&self, input: &str) -> Result<String, ToolError>;
}

// infrastructure/tools/web_search.rs
pub struct WebSearchTool {
    api_key: String,
}

#[async_trait]
impl Tool for WebSearchTool {
    fn name(&self) -> &str { "web_search" }
    fn description(&self) -> &str { "Search the web..." }
    async fn execute(&self, input: &str) -> Result<String, ToolError> {
        // Implementation
    }
}
```

## Agent System Guidelines

### State Machine
- Always model agent state explicitly
- Use enums for state representation
- Implement clear state transitions
- Add logging for all state changes

### Tool System
- All tools implement the `Tool` trait
- Tools are registered in a `ToolRegistry`
- Tool execution is sandboxed and time-limited
- Tool descriptions are used by LLM for selection

### Context Management
- Implement token counting
- Truncate conversation history intelligently
- Summarize old messages when context limit approached
- Include only relevant file contents

## Tauri Commands Best Practices

### Command Structure
```rust
#[tauri::command]
pub async fn send_message(
    state: State<'_, AppState>,
    message: String,
    conversation_id: Option<String>,
) -> Result<MessageResponse, String> {
    // 1. Validate input
    if message.trim().is_empty() {
        return Err("Message cannot be empty".to_string());
    }
    
    // 2. Call application layer
    let use_case = SendMessageUseCase::new(&state.agent, &state.repo);
    let response = use_case.execute(message, conversation_id)
        .await
        .map_err(|e| e.to_string())?;
    
    // 3. Return serialized response
    Ok(response)
}
```

### State Management
- Use `State<'_, AppState>` for global state
- Keep AppState minimal (connection pools, config)
- Avoid mutable state; prefer message passing

## Testing Guidelines

### Unit Tests
- Test all pure functions in `core/`
- Mock external dependencies
- Use `#[cfg(test)]` modules
- Test error cases explicitly

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_planner_creates_valid_plan() {
        let planner = Planner::new();
        let plan = planner.create_plan("Search for Rust resources").unwrap();
        assert!(plan.steps.len() > 0);
        assert_eq!(plan.steps[0].tool, "web_search");
    }
}
```

### Integration Tests
- Test tool implementations with mocked APIs
- Test database operations with temporary SQLite files
- Test LLM clients with mock servers

### E2E Tests
- Test critical user journeys
- Use actual Tauri app instance
- Test IPC communication

## Performance Considerations

### Database
- Use connection pooling
- Enable WAL mode for SQLite
- Create indexes for frequently queried columns
- Use prepared statements

### Async Performance
- Batch operations where possible
- Use `tokio::spawn` for CPU-intensive work
- Stream large responses (don't buffer in memory)
- Set appropriate timeouts

### Memory Management
- Use `Arc<T>` for shared state
- Avoid cloning large data structures
- Use streaming for LLM responses
- Implement conversation history limits

## Security Rules

### API
