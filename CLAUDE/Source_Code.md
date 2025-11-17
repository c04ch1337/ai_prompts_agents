# Digital Twin AI Desktop - Complete Source Code Generation Guide

## Overview
This guide will help you generate ALL source code files for the project and build for Windows, Ubuntu, and Web Server deployment.

---

## Phase 1: Project Initialization

### Step 1: Create Project Structure

```bash
# Create main project directory
mkdir digital-twin-desktop
cd digital-twin-desktop

# Initialize Git
git init
echo "# Digital Twin AI Desktop" > README.md

# Create directory structure
mkdir -p .cursorprompts
mkdir -p config
mkdir -p docs
mkdir -p scripts

# Backend structure
mkdir -p src-tauri/src/{core,application,infrastructure,api,config,utils}
mkdir -p src-tauri/src/core/{agent,reasoning,tools,models}
mkdir -p src-tauri/src/application/{use_cases,services}
mkdir -p src-tauri/src/infrastructure/{llm,tools,persistence,system}
mkdir -p src-tauri/src/infrastructure/persistence/{repositories,migrations}
mkdir -p src-tauri/src/api/{commands,handlers}
mkdir -p src-tauri/tests/{unit,integration,e2e}

# Frontend structure
mkdir -p src/{components,stores,hooks,api,types,styles}
mkdir -p src/components/{chat,agent,twin,common}
```

### Step 2: Initialize Rust Project

```bash
# Initialize Cargo workspace
cat > Cargo.toml << 'EOF'
[workspace]
members = ["src-tauri"]
resolver = "2"

[workspace.package]
version = "0.1.0"
edition = "2021"
authors = ["Your Name <your.email@example.com>"]
license = "MIT"

[workspace.dependencies]
tokio = { version = "1.35", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
EOF

# Initialize Tauri backend
cd src-tauri
cargo init --name digital-twin-desktop
cd ..
```

### Step 3: Initialize Frontend

```bash
# Create React + TypeScript project
npm create vite@latest src -- --template react-ts

# Navigate to src and install dependencies
cd src
npm install

# Install additional dependencies
npm install zustand @tanstack/react-query
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p

cd ..
```

---

## Phase 2: Generate Core Configuration Files

### 1. Root Configuration Files

#### .gitignore
```bash
cat > .gitignore << 'EOF'
# Rust
target/
**/*.rs.bk
Cargo.lock

# Node
node_modules/
dist/
dist-ssr/
*.local
.npm
.yarn

# Environment
.env.local
.env.*.local

# IDEs
.idea/
.vscode/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Build artifacts
src-tauri/target/
src/dist/

# Database
*.db
*.db-shm
*.db-wal

# Logs
*.log
EOF
```

#### .env.local.example
```bash
# Copy from the comprehensive template provided earlier
# (The 300+ line configuration file from the artifacts)
```

### 2. Cursor IDE Configuration Files

#### .cursorrules
```bash
# Copy the complete .cursorrules file from artifacts
```

#### .cursorprompts/architecture.md
```bash
# Copy from artifacts
```

#### .cursorprompts/agent-patterns.md
```bash
# Copy from artifacts
```

#### .cursorprompts/testing-guidelines.md
```bash
# Copy from artifacts
```

---

## Phase 3: Generate Rust Backend Source Code

### 1. Backend Cargo.toml

```bash
cat > src-tauri/Cargo.toml << 'EOF'
[package]
name = "digital-twin-desktop"
version = "0.1.0"
edition = "2021"
authors = ["Your Name <your.email@example.com>"]

[dependencies]
# Core Framework (Choose ONE deployment mode)
# Option A: Desktop mode with Tauri
tauri = { version = "2.0", features = ["shell-open"], optional = true }

# Option B: Web server mode
axum = { version = "0.7", features = ["ws", "macros"], optional = true }
tower = { version = "0.4", features = ["full"], optional = true }
tower-http = { version = "0.5", features = ["fs", "cors", "trace"], optional = true }

# Async Runtime
tokio = { version = "1.35", features = ["full"] }

# Serialization
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"

# Database
rusqlite = { version = "0.30", features = ["bundled"] }
sqlx = { version = "0.7", features = ["sqlite", "runtime-tokio-rustls"] }

# HTTP Client
reqwest = { version = "0.11", features = ["json", "stream"] }

# WebSocket
tokio-tungstenite = "0.21"

# Utilities
uuid = { version = "1.6", features = ["v4", "serde"] }
chrono = { version = "0.4", features = ["serde"] }
async-trait = "0.1"
futures = "0.3"
async-stream = "0.3"

# Error Handling
thiserror = "1.0"
anyhow = "1.0"

# Logging
tracing = "0.1"
tracing-subscriber = { version = "0.3", features = ["env-filter"] }

# Security
ring = "0.17"
base64 = "0.21"
argon2 = "0.5"

# Configuration
dotenv = "0.15"
config = "0.13"

# Digital Twin Specific (Optional - enable as needed)
# modbus = { version = "1.0", optional = true }
# rumqttc = { version = "0.23", optional = true }

[features]
default = ["desktop"]
desktop = ["tauri"]
server = ["axum", "tower", "tower-http"]
# iot = ["modbus", "rumqttc"]

[dev-dependencies]
mockito = "1.2"
tempfile = "3.8"

[build-dependencies]
tauri-build = { version = "2.0", features = [], optional = true }

[[bin]]
name = "digital-twin-desktop"
path = "src/main.rs"
EOF
```

### 2. Build Script (build.rs)

```bash
cat > src-tauri/build.rs << 'EOF'
fn main() {
    #[cfg(feature = "desktop")]
    {
        tauri_build::build()
    }
}
EOF
```

### 3. Tauri Configuration (tauri.conf.json)

```bash
cat > src-tauri/tauri.conf.json << 'EOF'
{
  "$schema": "https://schema.tauri.app/config/2.0.0",
  "build": {
    "beforeDevCommand": "cd ../src && npm run dev",
    "beforeBuildCommand": "cd ../src && npm run build",
    "devPath": "http://localhost:5173",
    "distDir": "../src/dist"
  },
  "package": {
    "productName": "Digital Twin AI Desktop",
    "version": "0.1.0"
  },
  "tauri": {
    "allowlist": {
      "all": false,
      "shell": {
        "all": false,
        "open": true
      },
      "dialog": {
        "all": true
      },
      "fs": {
        "all": true,
        "scope": ["$APPDATA/*", "$RESOURCE/*"]
      }
    },
    "bundle": {
      "active": true,
      "category": "DeveloperTool",
      "copyright": "Copyright © 2024",
      "identifier": "com.digitaltwin.ai",
      "icon": [
        "icons/32x32.png",
        "icons/128x128.png",
        "icons/128x128@2x.png",
        "icons/icon.icns",
        "icons/icon.ico"
      ],
      "longDescription": "",
      "resources": [],
      "shortDescription": "",
      "targets": "all",
      "windows": {
        "certificateThumbprint": null,
        "digestAlgorithm": "sha256",
        "timestampUrl": ""
      }
    },
    "security": {
      "csp": null
    },
    "updater": {
      "active": false
    },
    "windows": [
      {
        "fullscreen": false,
        "height": 800,
        "resizable": true,
        "title": "Digital Twin AI Desktop",
        "width": 1200,
        "minHeight": 600,
        "minWidth": 800
      }
    ]
  }
}
EOF
```

---

## Phase 4: Generate Core Source Files

### Core Layer Files

#### src-tauri/src/core/mod.rs
```rust
//! Core domain logic
//! 
//! This module contains pure business logic with zero external dependencies.

pub mod agent;
pub mod reasoning;
pub mod tools;
pub mod models;
pub mod errors;
```

#### src-tauri/src/core/errors.rs
```rust
//! Domain-specific error types

use thiserror::Error;

#[derive(Debug, Error)]
pub enum AgentError {
    #[error("Tool not found: {0}")]
    ToolNotFound(String),
    
    #[error("Tool execution failed: {0}")]
    ToolExecutionFailed(String),
    
    #[error("Invalid state transition: {0} -> {1}")]
    InvalidStateTransition(String, String),
    
    #[error("Context window exceeded: {0} tokens")]
    ContextWindowExceeded(usize),
    
    #[error("Planning failed: {0}")]
    PlanningFailed(String),
}

#[derive(Debug, Error)]
pub enum TwinError {
    #[error("Twin not found: {0}")]
    TwinNotFound(String),
    
    #[error("Synchronization failed: {0}")]
    SyncFailed(String),
    
    #[error("Simulation error: {0}")]
    SimulationError(String),
}
```

#### src-tauri/src/core/models/mod.rs
```rust
//! Domain models

pub mod conversation;
pub mod message;
pub mod digital_twin;
pub mod sensor_data;
pub mod simulation;

pub use conversation::*;
pub use message::*;
pub use digital_twin::*;
pub use sensor_data::*;
pub use simulation::*;
```

#### src-tauri/src/core/models/conversation.rs
```rust
//! Conversation domain model

use serde::{Deserialize, Serialize};
use uuid::Uuid;
use chrono::{DateTime, Utc};

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Conversation {
    pub id: ConversationId,
    pub title: String,
    pub created_at: DateTime<Utc>,
    pub updated_at: DateTime<Utc>,
    pub message_count: usize,
}

#[derive(Debug, Clone, Copy, PartialEq, Eq, Hash, Serialize, Deserialize)]
pub struct ConversationId(pub Uuid);

impl ConversationId {
    pub fn new() -> Self {
        Self(Uuid::new_v4())
    }
}

impl Default for ConversationId {
    fn default() -> Self {
        Self::new()
    }
}

impl std::fmt::Display for ConversationId {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        write!(f, "{}", self.0)
    }
}
```

#### src-tauri/src/core/models/message.rs
```rust
//! Message domain model

use serde::{Deserialize, Serialize};
use uuid::Uuid;
use chrono::{DateTime, Utc};
use super::ConversationId;

#[derive(Debug, Clone, Serialize, Deserialize)]
pub struct Message {
    pub id: MessageId,
    pub conversation_id: ConversationId,
    pub role: MessageRole,
    pub content: String,
    pub created_at: DateTime<Utc>,
    pub metadata: MessageMetadata,
}

#[derive(Debug, Clone, Copy, PartialEq, Eq, Hash, Serialize, Deserialize)]
pub struct MessageId(pub Uuid);

impl MessageId {
    pub fn new() -> Self {
        Self(Uuid::new_v4())
    }
}

impl Default for MessageId {
    fn default() -> Self {
        Self::new()
    }
}

#[derive(Debug, Clone, Copy, PartialEq, Eq, Serialize, Deserialize)]
#[serde(rename_all = "lowercase")]
pub enum MessageRole {
    System,
    User,
    Assistant,
}

#[derive(Debug, Clone, Default, Serialize, Deserialize)]
pub struct MessageMetadata {
    pub tokens: Option<usize>,
    pub tools_used: Vec<String>,
    pub reasoning: Option<String>,
}
```

#### src-tauri/src/core/tools/mod.rs
```rust
//! Tool abstractions

pub mod tool_trait;
pub mod registry;
pub mod safety;

pub use tool_trait::*;
pub use registry::*;
pub use safety::*;
```

#### src-tauri/src/core/tools/tool_trait.rs
```rust
//! Standard tool interface

use async_trait::async_trait;
use serde::{Deserialize, Serialize};
use std::time::Duration;
use thiserror::Error;

#[async_trait]
pub trait Tool: Send + Sync {
    fn name(&self) -> &str;
    fn description(&self) -> &str;
    fn input_schema(&self) -> serde_json::Value;
    async fn execute(&self, input: &str) -> Result<String, ToolError>;
    
    fn timeout(&self) -> Duration {
        Duration::from_secs(30)
    }
    
    fn requires_confirmation(&self) -> bool {
        false
    }
    
    fn safety_level(&self) -> SafetyLevel {
        SafetyLevel::Safe
    }
}

#[derive(Debug, Clone, Serialize, Deserialize)]
pub enum SafetyLevel {
    Safe,
    RequiresSandbox,
    Dangerous,
}

#[derive(Debug, Error)]
pub enum ToolError {
    #[error("Invalid input: {0}")]
    InvalidInput(String),
    
    #[error("Execution failed: {0}")]
    ExecutionFailed(String),
    
    #[error("Timeout after {0:?}")]
    Timeout(Duration),
    
    #[error("Permission denied: {0}")]
    PermissionDenied(String),
}
```

---

## Phase 5: Automated Code Generation Script

Create a script to generate ALL remaining files:

```bash
cat > scripts/generate_source.sh << 'EOF'
#!/bin/bash

# Digital Twin AI Desktop - Source Code Generator
# This script generates all boilerplate source files

set -e

PROJECT_ROOT="$(cd "$(dirname "${BASH_SOURCE[0]}")/.." && pwd)"
cd "$PROJECT_ROOT"

echo "🚀 Generating Digital Twin AI Desktop source code..."

# Function to create a Rust module file
create_rust_module() {
    local path=$1
    local module_name=$2
    local description=$3
    
    mkdir -p "$(dirname "$path")"
    
    cat > "$path" << RUST_EOF
//! $description

// TODO: Implement $module_name

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_placeholder() {
        // TODO: Add tests
    }
}
RUST_EOF
    
    echo "✅ Created: $path"
}

# Function to create mod.rs
create_mod_rs() {
    local path=$1
    shift
    local modules=("$@")
    
    cat > "$path" << 'MOD_EOF'
//! Module exports

MOD_EOF
    
    for module in "${modules[@]}"; do
        echo "pub mod $module;" >> "$path"
    done
    
    echo "" >> "$path"
    echo "// Re-exports" >> "$path"
    for module in "${modules[@]}"; do
        echo "pub use $module::*;" >> "$path"
    done
    
    echo "✅ Created: $path"
}

# Generate Core Layer
echo "📦 Generating Core Layer..."

create_rust_module "src-tauri/src/core/agent/orchestrator.rs" \
    "orchestrator" "Main agent orchestration logic"

create_rust_module "src-tauri/src/core/agent/state_machine.rs" \
    "state_machine" "Agent state management"

create_rust_module "src-tauri/src/core/agent/context_manager.rs" \
    "context_manager" "Context window management"

create_mod_rs "src-tauri/src/core/agent/mod.rs" \
    "orchestrator" "state_machine" "context_manager"

# Generate Application Layer
echo "📦 Generating Application Layer..."

create_rust_module "src-tauri/src/application/use_cases/send_message.rs" \
    "send_message" "Send message use case"

create_rust_module "src-tauri/src/application/use_cases/process_file.rs" \
    "process_file" "Process file use case"

create_mod_rs "src-tauri/src/application/use_cases/mod.rs" \
    "send_message" "process_file"

create_mod_rs "src-tauri/src/application/mod.rs" \
    "use_cases" "services"

# Generate Infrastructure Layer
echo "📦 Generating Infrastructure Layer..."

create_rust_module "src-tauri/src/infrastructure/llm/client_trait.rs" \
    "client_trait" "LLM client abstraction"

create_rust_module "src-tauri/src/infrastructure/llm/anthropic_client.rs" \
    "anthropic_client" "Anthropic Claude client"

create_mod_rs "src-tauri/src/infrastructure/llm/mod.rs" \
    "client_trait" "anthropic_client"

# Generate API Layer
echo "📦 Generating API Layer..."

create_rust_module "src-tauri/src/api/commands/conversation.rs" \
    "conversation" "Conversation commands"

create_mod_rs "src-tauri/src/api/commands/mod.rs" \
    "conversation"

create_mod_rs "src-tauri/src/api/mod.rs" \
    "commands" "handlers"

echo ""
echo "✅ Source code generation complete!"
echo "📝 Next steps:"
echo "   1. Review generated files"
echo "   2. Implement TODO sections"
echo "   3. Run: cargo build"
echo "   4. Run tests: cargo test"
EOF

chmod +x scripts/generate_source.sh
```

---

## Phase 6: Main Entry Point

### Desktop Version (main.rs)

```rust
// src-tauri/src/main.rs
#![cfg_attr(not(debug_assertions), windows_subsystem = "windows")]

#[cfg(feature = "desktop")]
mod desktop;

#[cfg(feature = "server")]
mod server;

fn main() {
    #[cfg(feature = "desktop")]
    desktop::run();
    
    #[cfg(feature = "server")]
    server::run();
}
```

### Desktop Module

```rust
// src-tauri/src/desktop.rs
use tauri::Manager;

mod core;
mod application;
mod infrastructure;
mod api;
mod config;
mod utils;

pub fn run() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![
            api::commands::conversation::list_conversations,
            api::commands::conversation::send_message,
        ])
        .setup(|app| {
            // Initialize app state
            Ok(())
        })
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

### Server Module

```rust
// src-tauri/src/server.rs
use axum::{Router, routing::get};
use std::net::SocketAddr;

mod core;
mod application;
mod infrastructure;
mod api;
mod config;
mod utils;

#[tokio::main]
pub async fn run() {
    tracing_subscriber::fmt::init();
    
    let app = Router::new()
        .route("/health", get(|| async { "OK" }));
    
    let addr = SocketAddr::from(([0, 0, 0, 0], 8080));
    println!("🚀 Server listening on http://{}", addr);
    
    axum::Server::bind(&addr)
        .serve(app.into_make_service())
        .await
        .expect("Failed to start server");
}
```

---

## Phase 7: Build Commands

### Build Scripts

```bash
cat > scripts/build.sh << 'EOF'
#!/bin/bash

# Build script for all platforms

set -e

echo "🔨 Building Digital Twin AI Desktop..."

# Build frontend
echo "📦 Building frontend..."
cd src
npm run build
cd ..

# Build backend - Desktop
echo "🖥️  Building desktop version..."
cd src-tauri
cargo build --release --features desktop
cd ..

# Build backend - Server
echo "🌐 Building server version..."
cd src-tauri
cargo build --release --features server --no-default-features
cd ..

echo "✅ Build complete!"
echo "Desktop binary: src-tauri/target/release/digital-twin-desktop"
echo "Server binary: src-tauri/target/release/digital-twin-desktop"
EOF

chmod +x scripts/build.sh
```

### Development Script

```bash
cat > scripts/dev.sh << 'EOF'
#!/bin/bash

# Development script

set -e

echo "🚀 Starting development mode..."

# Check if cargo-watch is installed
if ! command -v cargo-watch &> /dev/null; then
    echo "Installing cargo-watch..."
    cargo install cargo-watch
fi

# Start frontend dev server in background
cd src
npm run dev &
FRONTEND_PID=$!
cd ..

# Start backend with hot reload
cd src-tauri
cargo tauri dev
cd ..

# Cleanup on exit
trap "kill $FRONTEND_PID" EXIT
EOF

chmod +x scripts/dev.sh
```

---

## Phase 8: Cross-Platform Building

### Windows Build (on Windows or with cross-compilation)

```bash
# On Windows
cargo build --release --features desktop

# Cross-compile from Linux
rustup target add x86_64-pc-windows-gnu
cargo build --release --target x86_64-pc-windows-gnu --features desktop
```

### Linux Build

```bash
# Ubuntu/Debian dependencies
sudo apt install -y libwebkit2gtk-4.1-dev \
    build-essential \
    curl \
    wget \
    file \
    libssl-dev \
    libgtk-3-dev \
    libayatana-appindicator3-dev \
    librsvg2-dev

# Build
cargo build --release --features desktop
```

### Create Build Configuration

```bash
cat > .cargo/config.toml << 'EOF'
[build]
# Increase build parallelism
jobs = 8

[target.x86_64-pc-windows-gnu]
linker = "x86_64-w64-mingw32-gcc"
ar = "x86_64-w64-mingw32-ar"

[target.x86_64-unknown-linux-gnu]
linker = "gcc"

[profile.release]
opt-level = 3
lto = true
codegen-units = 1
strip = true
EOF
```

---

## Complete Build Process

### 1. One-Command Setup

```bash
cat > setup.sh << 'EOF'
#!/bin/bash

# Complete setup script

set -e

echo "🚀 Setting up Digital Twin AI Desktop..."

# Check prerequisites
command -v rustc >/dev/null 2>&1 || { echo "❌ Rust not installed. Install from https://rustup.rs/"; exit 1; }
command -v node >/dev/null 2>&1 || { echo "❌ Node.js not installed. Install from https://nodejs.org/"; exit 1; }

# Run generation script
./scripts/generate_source.sh

# Copy configuration
cp .env.local.example .env.local
echo "📝 Edit .env.local with your API keys"

# Install frontend dependencies
cd src
npm install
cd ..

# Install Rust dependencies
cd src-tauri
cargo fetch
cd ..

echo "✅ Setup complete!"
echo "📝 Next: Edit .env.local, then run: ./scripts/dev.sh"
EOF

chmod +x setup.sh
```

### 2. Execute Setup

```bash
# Clone/create project
./setup.sh

# Edit configuration
nano .env.local

# Start development
./scripts/dev.sh

# Or build for production
./scripts/build.sh
```

---

## Phase 9: Package for Distribution

### Create Installer (Windows)

```bash
# Tauri handles this automatically
cargo tauri build

# Output: src-tauri/target/release/bundle/msi/
```

### Create .deb Package (Ubuntu)

```bash
cargo tauri build

# Output: src-tauri/target/release/bundle/deb/
```

### Create AppImage (Linux)

```bash
cargo tauri build

# Output: src-tauri/target/release/bundle/appimage/
```

---

## Summary: Complete Command Sequence

```bash
# 1. Initial setup
git clone <your-repo> digital-twin-desktop
cd digital-twin-desktop

# 2. Run setup script
./setup.sh

# 3. Configure environment
nano .env.local  # Add your API keys

# 4. Develop
./scripts/dev.sh

# 5. Build for production
./scripts/build.sh

# 6. Package for distribution
cd src-tauri
cargo tauri build
```

---

## Platform-Specific Notes

### Windows
- **Desktop**: Full Tauri app with native Windows UI
- **Installer**: `.msi` installer generated automatically
- **Requirements**: Windows 10+, WebView2 (auto-installed)

### Ubuntu/Linux
- **Desktop**: Tauri app with GTK WebView
- **Packages**: `.deb`, `.AppImage`, or standalone binary
- **Server**: Run headless with systemd service
- **Requirements**: Ubuntu 20.04+

### Cross-Platform
- Same Rust codebase for all platforms
- Same React frontend
- Platform-specific builds handled by Cargo
- Tauri handles platform UI differences

**Result**: One codebase → Windows .exe, Linux .deb/.AppImage, or Web Server
