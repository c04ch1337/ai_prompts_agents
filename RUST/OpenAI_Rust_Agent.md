Nice, we’re into “immortal core architecture” territory. Let’s build you a **Local Master Rust repo** that:

* Does **only** three things:

  1. Understands tasks in a stable domain model.
  2. Routes those tasks to pluggable backends (cloud gateways, memory, UI).
  3. Never bakes in any specific AI vendor, UI, or prompt profile.

Everything else is **plug-and-play modules** around it.

Below is a full template repo you can copy-paste into `local-master/`. It’s opinionated but intentionally minimal and stable enough that you should *never* need to refactor the core — only add new adapters.

---

# 1. Repo layout

```text
local-master/
├─ Cargo.toml
├─ src/
│  ├─ main.rs
│  ├─ lib.rs
│  ├─ core/
│  │   ├─ mod.rs
│  │   ├─ ids.rs
│  │   ├─ error.rs
│  │   ├─ task.rs
│  ├─ ports/
│  │   ├─ mod.rs
│  │   ├─ cloud_gateway.rs
│  │   ├─ memory_store.rs
│  │   ├─ ui_frontend.rs
│  ├─ services/
│  │   ├─ mod.rs
│  │   ├─ master.rs
│  ├─ adapters/
│  │   ├─ mod.rs
│  │   ├─ cloud_http.rs
│  │   ├─ memory_filesystem.rs
│  │   ├─ ui_cli.rs
│
└─ config/
   └─ default_master.json
```

* `core/` = the eternal soul. Only touch to add *new* concepts; don’t break existing ones.
* `ports/` = stable traits (“ports”) that core depends on.
* `services/` = orchestrating logic using the ports.
* `adapters/` = concrete implementations: HTTP to cloud, filesystem memory, CLI.

You can swap any adapter without changing `core` or `services`.

---

# 2. `Cargo.toml`

```toml
[package]
name = "local-master"
version = "0.1.0"
edition = "2021"

[dependencies]
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }
serde_json = "1"
uuid = { version = "1", features = ["v4", "serde"] }
thiserror = "1"
reqwest = { version = "0.12", features = ["json", "rustls-tls"] }
clap = { version = "4", features = ["derive"] }
chrono = { version = "0.4", features = ["serde"] }
```

---

# 3. Core: immortal types

## `src/core/mod.rs`

```rust
pub mod ids;
pub mod error;
pub mod task;
```

---

## `src/core/ids.rs`

```rust
use serde::{Deserialize, Serialize};
use uuid::Uuid;

/// Globally unique ID for a Task.
#[derive(Debug, Clone, Copy, Serialize, Deserialize, PartialEq, Eq, Hash)]
pub struct TaskId(Uuid);

impl TaskId {
    pub fn new() -> Self {
        Self(Uuid::new_v4())
    }

    pub fn from_uuid(u: Uuid) -> Self {
        Self(u)
    }

    pub fn as_uuid(&self) -> Uuid {
        self.0
    }

    pub fn to_string(&self) -> String {
        self.0.to_string()
    }
}

/// Logical identifier of a prompt profile / sub-agent.
/// Example: "kids_story_book_youtube", "ai_bug_triage".
#[derive(Debug, Clone, Serialize, Deserialize, PartialEq, Eq, Hash)]
pub struct PromptProfileId(pub String);

impl PromptProfileId {
    pub fn new<S: Into<String>>(s: S) -> Self {
        Self(s.into())
    }
}

/// Logical identifier of a provider (Gemini, OpenRouter, Local-Ollama, etc.).
#[derive(Debug, Clone, Serialize, Deserialize, PartialEq, Eq, Hash)]
pub struct ProviderId(pub String);

impl ProviderId {
    pub fn new<S: Into<String>>(s: S) -> Self {
        Self(s.into())
    }
}
```

---

## `src/core/error.rs`

```rust
use thiserror::Error;

#[derive(Debug, Error)]
pub enum MasterError {
    #[error("Configuration error: {0}")]
    Config(String),

    #[error("Network error: {0}")]
    Network(String),

    #[error("Serialization error: {0}")]
    Serialization(String),

    #[error("Storage error: {0}")]
    Storage(String),

    #[error("Upstream error: {0}")]
    Upstream(String),

    #[error("Unknown error: {0}")]
    Unknown(String),
}

pub type MasterResult<T> = Result<T, MasterError>;
```

---

## `src/core/task.rs`

```rust
use crate::core::ids::{PromptProfileId, ProviderId, TaskId};
use chrono::{DateTime, Utc};
use serde::{Deserialize, Serialize};
use serde_json::Value as JsonValue;

/// Stable representation of a "thing the Master is asked to do".
/// This is the core contract between UI, orchestrator logic, and cloud gateway.
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct TaskRequest {
    pub task_id: TaskId,
    pub prompt_profile_id: PromptProfileId,
    pub provider: Option<ProviderId>,
    pub model: Option<String>,
    pub input_payload: JsonValue,
    pub session_context: SessionContext,
    pub created_at: DateTime<Utc>,
}

/// Lightweight context about who's asking and what environment we're in.
#[derive(Debug, Clone, Serialize, Deserialize, Default)]
pub struct SessionContext {
    pub user_id: Option<String>,
    pub locale: Option<String>,
    pub tags: Vec<String>,
}

/// What comes back from the cloud prompt agent.
#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct TaskResponse {
    pub task_id: TaskId,
    pub status: TaskStatus,
    /// Primary, human-readable result (markdown / text).
    pub output_text: Option<String>,
    /// Machine-readable result (JSON).
    pub output_json: Option<JsonValue>,
    /// Any metadata (tokens, model, etc.).
    pub metadata: JsonValue,
    pub completed_at: DateTime<Utc>,
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub enum TaskStatus {
    Success,
    Failed { reason: String },
}

impl TaskRequest {
    pub fn new(
        prompt_profile_id: PromptProfileId,
        provider: Option<ProviderId>,
        model: Option<String>,
        input_payload: JsonValue,
        session_context: SessionContext,
    ) -> Self {
        Self {
            task_id: TaskId::new(),
            prompt_profile_id,
            provider,
            model,
            input_payload,
            session_context,
            created_at: Utc::now(),
        }
    }
}
```

---

# 4. Ports: stable interfaces (plugin points)

## `src/ports/mod.rs`

```rust
pub mod cloud_gateway;
pub mod memory_store;
pub mod ui_frontend;
```

---

## `src/ports/cloud_gateway.rs`

```rust
use crate::core::error::MasterResult;
use crate::core::task::{TaskRequest, TaskResponse};

/// Port for talking to the remote Prompt Gateway / Docker swarm.
/// Local Master doesn't care if it's Gemini, OpenRouter, or a potato.
/// It just sends TaskRequest, gets TaskResponse.
#[async_trait::async_trait]
pub trait CloudGateway: Send + Sync {
    async fn execute_task(&self, req: TaskRequest) -> MasterResult<TaskResponse>;
}
```

> Note: This uses `async_trait`. We forgot to add it as dep — add to `Cargo.toml`:

```toml
async-trait = "0.1"
```

---

## `src/ports/memory_store.rs`

```rust
use crate::core::error::MasterResult;
use crate::core::ids::TaskId;
use crate::core::task::{TaskRequest, TaskResponse};

/// Generic storage port. Could be filesystem, sqlite, S3, whatever.
#[async_trait::async_trait]
pub trait MemoryStore: Send + Sync {
    async fn save_request(&self, req: &TaskRequest) -> MasterResult<()>;
    async fn save_response(&self, res: &TaskResponse) -> MasterResult<()>;
    async fn get_response(&self, task_id: TaskId) -> MasterResult<Option<TaskResponse>>;
}
```

---

## `src/ports/ui_frontend.rs`

This is optional, but we define a tiny trait so future GUI/TUI frontends can share logic.

```rust
use crate::core::error::MasterResult;
use crate::core::task::{TaskRequest, TaskResponse};

#[async_trait::async_trait]
pub trait UiFrontend {
    async fn handle_interaction(&self, req: TaskRequest) -> MasterResult<TaskResponse>;
}
```

---

# 5. Service: the Master brain (but minimal)

## `src/services/mod.rs`

```rust
pub mod master;
```

---

## `src/services/master.rs`

```rust
use std::sync::Arc;

use crate::core::error::{MasterError, MasterResult};
use crate::core::task::{TaskRequest, TaskResponse};
use crate::ports::cloud_gateway::CloudGateway;
use crate::ports::memory_store::MemoryStore;

/// Core "Master Service".
/// Responsibilities:
/// - Validate & log tasks.
/// - Send them to cloud gateway.
/// - Persist results via MemoryStore.
/// - Return the response.
/// That's it. No vendor-specific logic here.
pub struct MasterService {
    cloud_gateway: Arc<dyn CloudGateway>,
    memory_store: Arc<dyn MemoryStore>,
}

impl MasterService {
    pub fn new(
        cloud_gateway: Arc<dyn CloudGateway>,
        memory_store: Arc<dyn MemoryStore>,
    ) -> Self {
        Self {
            cloud_gateway,
            memory_store,
        }
    }

    pub async fn execute(&self, req: TaskRequest) -> MasterResult<TaskResponse> {
        // Persist the incoming request (fire-and-forget but we await for simplicity).
        self.memory_store
            .save_request(&req)
            .await
            .map_err(|e| MasterError::Storage(format!("failed to save request: {e}")))?;

        // Forward to cloud gateway.
        let res = self.cloud_gateway.execute_task(req).await?;

        // Persist response.
        self.memory_store
            .save_response(&res)
            .await
            .map_err(|e| MasterError::Storage(format!("failed to save response: {e}")))?;

        Ok(res)
    }
}
```

---

# 6. Adapters: pluggable implementations

## `src/adapters/mod.rs`

```rust
pub mod cloud_http;
pub mod memory_filesystem;
pub mod ui_cli;
```

---

## `src/adapters/cloud_http.rs`

Simple HTTP adapter from `CloudGateway` → your remote Prompt Gateway.

```rust
use std::time::Duration;

use crate::core::error::{MasterError, MasterResult};
use crate::core::task::{TaskRequest, TaskResponse};
use crate::ports::cloud_gateway::CloudGateway;
use async_trait::async_trait;
use reqwest::Client;
use serde::{Deserialize, Serialize};

#[derive(Clone)]
pub struct HttpCloudGateway {
    client: Client,
    base_url: String,
    api_key: Option<String>,
}

impl HttpCloudGateway {
    pub fn new<S: Into<String>>(base_url: S, api_key: Option<String>) -> Self {
        let client = Client::builder()
            .timeout(Duration::from_secs(60))
            .build()
            .expect("failed to build reqwest client");

        Self {
            client,
            base_url: base_url.into(),
            api_key,
        }
    }
}

#[derive(Debug, Serialize, Deserialize)]
struct GatewayRequest {
    task: TaskRequest,
}

#[derive(Debug, Serialize, Deserialize)]
struct GatewayResponse {
    task: TaskResponse,
}

#[async_trait]
impl CloudGateway for HttpCloudGateway {
    async fn execute_task(&self, req: TaskRequest) -> MasterResult<TaskResponse> {
        let wrapped = GatewayRequest { task: req };

        let mut http_req = self
            .client
            .post(format!("{}/api/run", self.base_url))
            .json(&wrapped);

        if let Some(ref key) = self.api_key {
            http_req = http_req.bearer_auth(key);
        }

        let res = http_req.send().await.map_err(|e| {
            MasterError::Network(format!("cloud gateway HTTP error: {e}"))
        })?;

        if !res.status().is_success() {
            let status = res.status();
            let body = res.text().await.unwrap_or_default();
            return Err(MasterError::Upstream(format!(
                "cloud gateway returned {status}: {body}"
            )));
        }

        let gw_res: GatewayResponse = res
            .json()
            .await
            .map_err(|e| MasterError::Serialization(format!("invalid gateway JSON: {e}")))?;

        Ok(gw_res.task)
    }
}
```

---

## `src/adapters/memory_filesystem.rs`

Very simple JSON file store. Not “database good”, but resilient and easy to understand; you can swap it later.

```rust
use std::fs;
use std::path::{Path, PathBuf};
use std::sync::Arc;

use crate::core::error::{MasterError, MasterResult};
use crate::core::ids::TaskId;
use crate::core::task::{TaskRequest, TaskResponse};
use crate::ports::memory_store::MemoryStore;
use async_trait::async_trait;
use serde_json::to_string_pretty;
use tokio::task;

#[derive(Clone)]
pub struct FilesystemMemoryStore {
    root: Arc<PathBuf>,
}

impl FilesystemMemoryStore {
    pub fn new<P: Into<PathBuf>>(root: P) -> Self {
        let root = root.into();
        fs::create_dir_all(&root).expect("failed to create memory store directory");
        Self {
            root: Arc::new(root),
        }
    }

    fn request_path(&self, task_id: TaskId) -> PathBuf {
        self.root.join(format!("{}-req.json", task_id.to_string()))
    }

    fn response_path(&self, task_id: TaskId) -> PathBuf {
        self.root.join(format!("{}-res.json", task_id.to_string()))
    }
}

#[async_trait]
impl MemoryStore for FilesystemMemoryStore {
    async fn save_request(&self, req: &TaskRequest) -> MasterResult<()> {
        let path = self.request_path(req.task_id);
        let data = to_string_pretty(req).map_err(|e| {
            MasterError::Serialization(format!("failed to serialize request: {e}"))
        })?;

        let path_clone = path.clone();
        task::spawn_blocking(move || {
            fs::write(path_clone, data)
                .map_err(|e| MasterError::Storage(format!("failed to write request: {e}")))
        })
        .await
        .map_err(|e| MasterError::Storage(format!("join error: {e}")))??;

        Ok(())
    }

    async fn save_response(&self, res: &TaskResponse) -> MasterResult<()> {
        let path = self.response_path(res.task_id);
        let data = to_string_pretty(res).map_err(|e| {
            MasterError::Serialization(format!("failed to serialize response: {e}"))
        })?;

        let path_clone = path.clone();
        task::spawn_blocking(move || {
            fs::write(path_clone, data)
                .map_err(|e| MasterError::Storage(format!("failed to write response: {e}")))
        })
        .await
        .map_err(|e| MasterError::Storage(format!("join error: {e}")))??;

        Ok(())
    }

    async fn get_response(&self, task_id: TaskId) -> MasterResult<Option<TaskResponse>> {
        let path = self.response_path(task_id);
        let path_clone = path.clone();

        let result = task::spawn_blocking(move || {
            if !Path::new(&path_clone).exists() {
                return Ok(None);
            }
            let data = fs::read_to_string(&path_clone)
                .map_err(|e| MasterError::Storage(format!("failed to read response: {e}")))?;
            let parsed: TaskResponse = serde_json::from_str(&data).map_err(|e| {
                MasterError::Serialization(format!("failed to parse response JSON: {e}"))
            })?;
            Ok(Some(parsed))
        })
        .await
        .map_err(|e| MasterError::Storage(format!("join error: {e}")))??;

        Ok(result)
    }
}
```

---

## `src/adapters/ui_cli.rs`

This is a minimal CLI front-end that:

* Accepts a prompt profile ID.
* Accepts JSON input.
* Creates a `TaskRequest`.
* Calls `MasterService`.

```rust
use std::sync::Arc;

use clap::{Parser, Subcommand};
use serde_json::Value as JsonValue;

use crate::core::ids::{PromptProfileId, ProviderId};
use crate::core::task::{SessionContext, TaskRequest};
use crate::services::master::MasterService;

#[derive(Parser, Debug)]
#[command(name = "local-master")]
#[command(about = "Local Master Orchestrator CLI", long_about = None)]
pub struct Cli {
    #[command(subcommand)]
    command: Commands,
}

#[derive(Subcommand, Debug)]
pub enum Commands {
    /// Execute a single task against a prompt profile.
    RunTask {
        /// Prompt profile ID (e.g. 'kids_story_book_youtube')
        #[arg(short, long)]
        profile: String,

        /// Provider ID override (optional, e.g. 'gemini', 'openrouter')
        #[arg(short = 'p', long)]
        provider: Option<String>,

        /// Model name override (optional, e.g. 'gemini-2.0-pro')
        #[arg(short = 'm', long)]
        model: Option<String>,

        /// JSON string with the input payload.
        /// Example: '{"age_range":"5-7","theme":"courage","main_character":"Luna"}'
        #[arg(short = 'i', long)]
        input: String,

        /// Optional user id
        #[arg(long)]
        user_id: Option<String>,
    },
}

pub async fn run_cli(master: Arc<MasterService>) -> anyhow::Result<()> {
    let cli = Cli::parse();

    match cli.command {
        Commands::RunTask {
            profile,
            provider,
            model,
            input,
            user_id,
        } => {
            let input_payload: JsonValue = serde_json::from_str(&input)?;
            let session_context = SessionContext {
                user_id,
                locale: Some("en-US".to_string()),
                tags: vec!["cli".to_string()],
            };

            let req = TaskRequest::new(
                PromptProfileId::new(profile),
                provider.map(ProviderId::new),
                model,
                input_payload,
                session_context,
            );

            let res = master.execute(req).await?;

            println!("Status: {:?}", res.status);
            if let Some(text) = res.output_text {
                println!("Output Text:\n{:#}", text);
            }
            if let Some(json) = res.output_json {
                println!("Output JSON:\n{}", serde_json::to_string_pretty(&json)?);
            }

            Ok(())
        }
    }
}
```

---

# 7. Library + main wiring

## `src/lib.rs`

```rust
pub mod core;
pub mod ports;
pub mod services;
pub mod adapters;
```

---

## `src/main.rs`

This is the only file that knows about specific adapters and environment variables. You can rip it out and replace it for GUI/TUI, but the **core** stays the same.

```rust
mod core;
mod ports;
mod services;
mod adapters;

use std::env;
use std::sync::Arc;

use adapters::cloud_http::HttpCloudGateway;
use adapters::memory_filesystem::FilesystemMemoryStore;
use adapters::ui_cli::run_cli;
use services::master::MasterService;
use tokio::runtime::Builder;

#[tokio::main]
async fn main() -> anyhow::Result<()> {
    // In the future you can load this from config/default_master.json or env.
    let base_url = env::var("CLOUD_GATEWAY_URL")
        .unwrap_or_else(|_| "https://prompt-gateway.example.com".to_string());

    let api_key = env::var("CLOUD_GATEWAY_API_KEY").ok();

    let gateway = HttpCloudGateway::new(base_url, api_key);
    let memory_store = FilesystemMemoryStore::new("./master_memory");

    let master = MasterService::new(
        Arc::new(gateway),
        Arc::new(memory_store),
    );
    let master = Arc::new(master);

    run_cli(master).await
}
```

---

# 8. Example config file (optional)

`config/default_master.json` — purely illustrative, not wired yet, but gives a stable future format.

```json
{
  "cloud_gateway_url": "https://prompt-gateway.example.com",
  "memory_root": "./master_memory",
  "default_locale": "en-US"
}
```

Later you can add a `config.rs` adapter to load this and keep `core` untouched.

---

# 9. Why this core can last “forever”

* **Core types are generic**: `TaskRequest` and `TaskResponse` use `serde_json::Value` for payloads and metadata, so you can evolve what you send to cloud without changing the shape.
* **Only interfaces at the boundary**:

  * `CloudGateway` is “send TaskRequest, get TaskResponse”.
  * `MemoryStore` is “save/get Task*”.
* **Everything specific** (Docker, VEO3, Gemini, OpenRouter, whatever the AI deity of 2035 is) lives in:

  * Prompt profiles (data)
  * Cloud Gateway implementation (adapters)
  * Remote containers.

Your Local Master doesn’t know or care if:

* A prompt becomes a full multi-agent DAG.
* The remote side switches from REST to gRPC.
* Providers rotate.

You can replicate this Master **a million times** and all they need is different:

* `CLOUD_GATEWAY_URL`
* `CLOUD_GATEWAY_API_KEY`
* `master_memory` directory

…and they’ll still accept the same `TaskRequest` → `TaskResponse` semantics.

---

If you’d like a next move, I’d take **one** of your 50 prompt profiles (say `kids_story_book_youtube`) and sketch the **matching cloud-side Docker agent** that speaks exactly this `GatewayRequest/GatewayResponse` contract, so your General can start barking orders at the swarm.
