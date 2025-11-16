# Digital Twin AI Desktop - Headless Server Deployment Guide

## Overview
Yes, you can absolutely run this application on Ubuntu Server (headless) or Ubuntu Workstation without a desktop environment. The Tauri application can be split into two deployment modes.

---

## Deployment Architectures

### Architecture 1: Headless Web Server (Recommended for Servers)

```
┌─────────────────────────────────────────────┐
│         Ubuntu Server (No Desktop)          │
│                                             │
│  ┌───────────────────────────────────────┐ │
│  │   Rust Backend (Actix/Axum/Warp)      │ │
│  │   - Agent orchestrator                 │ │
│  │   - Tool execution                     │ │
│  │   - LLM integration                    │ │
│  │   - Digital twin logic                 │ │
│  │   - Database (SQLite)                  │ │
│  │                                         │ │
│  │   Listening on: 0.0.0.0:8080          │ │
│  └───────────────────────────────────────┘ │
│                     ↕                       │
│  ┌───────────────────────────────────────┐ │
│  │   Static Frontend Build               │ │
│  │   - React app (compiled)              │ │
│  │   - Served via Rust web server        │ │
│  └───────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
                      ↕
              HTTP/WebSocket
                      ↕
┌─────────────────────────────────────────────┐
│    Client Browser (Any Machine)            │
│    http://server-ip:8080                   │
└─────────────────────────────────────────────┘
```

**Benefits**:
- No desktop environment needed
- Accessible from any device via browser
- Centralized deployment
- Multiple simultaneous users possible
- Lower resource usage on server

---

### Architecture 2: Tauri Desktop on Server with X11 Forwarding

```
┌─────────────────────────────────────────────┐
│         Ubuntu Server (with X11)           │
│                                             │
│  ┌───────────────────────────────────────┐ │
│  │   Tauri Application                    │ │
│  │   - Rust backend                       │ │
│  │   - WebView UI                         │ │
│  │   - Display via X11                    │ │
│  └───────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
                      ↕
                  X11 Protocol
                      ↕
┌─────────────────────────────────────────────┐
│    Client Machine (with X server)          │
│    ssh -X user@server                      │
└─────────────────────────────────────────────┘
```

**Benefits**:
- Original Tauri app runs unchanged
- Full desktop app experience
- Single user at a time

**Drawbacks**:
- Requires X11 forwarding
- More latency
- Less efficient

---

## Recommended Solution: Web Server Mode

Convert the Tauri desktop app into a web server that serves the same frontend and backend logic.

### Project Structure Modifications

```
digital-twin-desktop/
├── src-tauri/              # Rust backend (unchanged)
│   └── src/
│       ├── core/           # Same core logic
│       ├── application/    # Same use cases
│       ├── infrastructure/ # Same infrastructure
│       ├── api/            # Change from Tauri to HTTP
│       └── main.rs         # New: Web server instead of Tauri
│
├── src/                    # React frontend (mostly unchanged)
│   ├── api/                # Change: HTTP calls instead of Tauri invoke
│   └── ...
│
└── Cargo.toml              # Replace Tauri with Axum/Actix
```

---

## Implementation Guide

### 1. Update Cargo.toml for Web Server

Replace Tauri dependencies with web server framework:

```toml
[dependencies]
# Remove: tauri = "2.0"

# Add web server framework (choose one):
axum = { version = "0.7", features = ["ws", "macros"] }
tokio = { version = "1.35", features = ["full"] }
tower = { version = "0.4", features = ["full"] }
tower-http = { version = "0.5", features = ["fs", "cors", "trace"] }

# Keep all existing dependencies:
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
rusqlite = { version = "0.30", features = ["bundled"] }
# ... rest of dependencies unchanged
```

### 2. Replace Tauri Commands with HTTP Endpoints

**Before (Tauri):**
```rust
// src-tauri/src/api/commands/conversation.rs
#[tauri::command]
pub async fn send_message(
    state: State<'_, AppState>,
    message: String,
) -> Result<MessageResponse, String> {
    // Implementation
}
```

**After (Axum):**
```rust
// src-tauri/src/api/handlers/conversation.rs
use axum::{
    extract::State,
    Json,
    response::IntoResponse,
};

pub async fn send_message(
    State(app_state): State<AppState>,
    Json(payload): Json<SendMessageRequest>,
) -> impl IntoResponse {
    // Same implementation, different signature
    match use_case.execute(payload.message).await {
        Ok(response) => Json(response).into_response(),
        Err(e) => (
            StatusCode::INTERNAL_SERVER_ERROR,
            Json(json!({ "error": e.to_string() }))
        ).into_response(),
    }
}

#[derive(Deserialize)]
pub struct SendMessageRequest {
    pub message: String,
    pub conversation_id: Option<String>,
}
```

### 3. Create Web Server Main

```rust
// src-tauri/src/main.rs
use axum::{
    routing::{get, post},
    Router,
};
use tower_http::{
    services::ServeDir,
    cors::CorsLayer,
    trace::TraceLayer,
};
use std::net::SocketAddr;

mod core;
mod application;
mod infrastructure;
mod api;
mod config;
mod utils;

#[tokio::main]
async fn main() {
    // Initialize logging
    tracing_subscriber::fmt::init();

    // Load configuration
    let config = config::load_config().expect("Failed to load config");

    // Initialize database
    let db_pool = infrastructure::persistence::create_pool(&config.database)
        .await
        .expect("Failed to create database pool");

    // Run migrations
    infrastructure::persistence::run_migrations(&db_pool)
        .await
        .expect("Failed to run migrations");

    // Initialize services
    let tool_registry = Arc::new(infrastructure::tools::create_registry());
    let llm_client = infrastructure::llm::create_client(&config.llm);
    let agent = core::agent::Orchestrator::new(tool_registry.clone());

    // Create shared application state
    let app_state = AppState {
        db_pool,
        agent: Arc::new(agent),
        tool_registry,
        llm_client: Arc::new(llm_client),
        config: Arc::new(config),
    };

    // Build API routes
    let api_routes = Router::new()
        .route("/api/conversations", get(api::handlers::list_conversations))
        .route("/api/conversations", post(api::handlers::create_conversation))
        .route("/api/messages", post(api::handlers::send_message))
        .route("/api/messages/stream", get(api::handlers::stream_message))
        .route("/api/tools", get(api::handlers::list_tools))
        .route("/api/tools/execute", post(api::handlers::execute_tool))
        .route("/api/twins", get(api::handlers::list_twins))
        .route("/api/twins/:id", get(api::handlers::get_twin))
        .route("/api/twins/:id/sync", post(api::handlers::sync_twin))
        .route("/api/simulations", post(api::handlers::start_simulation))
        .route("/api/health", get(health_check))
        .with_state(app_state);

    // Serve static frontend files
    let static_files = ServeDir::new("dist")
        .not_found_service(ServeDir::new("dist/index.html"));

    // Combine routes
    let app = Router::new()
        .nest("/", api_routes)
        .fallback_service(static_files)
        .layer(CorsLayer::permissive())
        .layer(TraceLayer::new_for_http());

    // Start server
    let addr = SocketAddr::from(([0, 0, 0, 0], 8080));
    println!("🚀 Server listening on http://{}", addr);
    println!("📊 API available at http://{}/api", addr);

    axum::Server::bind(&addr)
        .serve(app.into_make_service())
        .await
        .expect("Failed to start server");
}

async fn health_check() -> &'static str {
    "OK"
}

#[derive(Clone)]
pub struct AppState {
    pub db_pool: SqlitePool,
    pub agent: Arc<core::agent::Orchestrator>,
    pub tool_registry: Arc<infrastructure::tools::ToolRegistry>,
    pub llm_client: Arc<dyn infrastructure::llm::LlmClient>,
    pub config: Arc<config::AppConfig>,
}
```

### 4. Update Frontend API Calls

**Before (Tauri invoke):**
```typescript
// src/api/chat.ts
import { invoke } from '@tauri-apps/api/tauri';

export async function sendMessage(message: string): Promise<MessageResponse> {
  return await invoke('send_message', { message });
}
```

**After (HTTP fetch):**
```typescript
// src/api/chat.ts
const API_BASE = import.meta.env.VITE_API_URL || 'http://localhost:8080';

export async function sendMessage(message: string): Promise<MessageResponse> {
  const response = await fetch(`${API_BASE}/api/messages`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ message }),
  });
  
  if (!response.ok) {
    throw new Error(`HTTP error! status: ${response.status}`);
  }
  
  return await response.json();
}
```

### 5. WebSocket Streaming for Agent Responses

```rust
// src-tauri/src/api/handlers/streaming.rs
use axum::{
    extract::{
        ws::{WebSocket, WebSocketUpgrade, Message},
        State,
    },
    response::IntoResponse,
};

pub async fn stream_message(
    ws: WebSocketUpgrade,
    State(app_state): State<AppState>,
) -> impl IntoResponse {
    ws.on_upgrade(|socket| handle_socket(socket, app_state))
}

async fn handle_socket(mut socket: WebSocket, app_state: AppState) {
    while let Some(msg) = socket.recv().await {
        if let Ok(Message::Text(text)) = msg {
            // Parse incoming message
            let request: SendMessageRequest = match serde_json::from_str(&text) {
                Ok(req) => req,
                Err(e) => {
                    let _ = socket.send(Message::Text(
                        json!({"error": e.to_string()}).to_string()
                    )).await;
                    continue;
                }
            };

            // Stream response chunks
            let mut stream = app_state.agent.stream_response(request.message).await;
            
            while let Some(chunk) = stream.next().await {
                match chunk {
                    Ok(text) => {
                        let msg = json!({
                            "type": "chunk",
                            "content": text
                        });
                        if socket.send(Message::Text(msg.to_string())).await.is_err() {
                            break;
                        }
                    }
                    Err(e) => {
                        let msg = json!({
                            "type": "error",
                            "error": e.to_string()
                        });
                        let _ = socket.send(Message::Text(msg.to_string())).await;
                        break;
                    }
                }
            }

            // Send completion message
            let msg = json!({"type": "complete"});
            let _ = socket.send(Message::Text(msg.to_string())).await;
        }
    }
}
```

**Frontend WebSocket client:**
```typescript
// src/hooks/useStreaming.ts
export function useStreaming() {
  const connect = (message: string, onChunk: (text: string) => void) => {
    const ws = new WebSocket(`ws://localhost:8080/api/messages/stream`);
    
    ws.onopen = () => {
      ws.send(JSON.stringify({ message }));
    };
    
    ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      
      if (data.type === 'chunk') {
        onChunk(data.content);
      } else if (data.type === 'complete') {
        ws.close();
      } else if (data.type === 'error') {
        console.error('Streaming error:', data.error);
        ws.close();
      }
    };
    
    return ws;
  };
  
  return { connect };
}
```

---

## Deployment Steps for Ubuntu Server

### 1. Install Dependencies

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env

# Install build dependencies
sudo apt install -y build-essential pkg-config libssl-dev

# Install Node.js for frontend build
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

### 2. Build the Application

```bash
# Clone your repository
git clone <your-repo>
cd digital-twin-desktop

# Build frontend
cd src
npm install
npm run build
cd ..

# Build Rust backend
cargo build --release

# Copy frontend build to expected location
cp -r src/dist ./dist
```

### 3. Create Systemd Service

```bash
sudo nano /etc/systemd/system/digital-twin-ai.service
```

```ini
[Unit]
Description=Digital Twin AI Desktop Server
After=network.target

[Service]
Type=simple
User=your-username
WorkingDirectory=/path/to/digital-twin-desktop
Environment="RUST_LOG=info"
EnvironmentFile=/path/to/digital-twin-desktop/.env.local
ExecStart=/path/to/digital-twin-desktop/target/release/digital-twin-desktop
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

### 4. Start and Enable Service

```bash
# Reload systemd
sudo systemctl daemon-reload

# Start service
sudo systemctl start digital-twin-ai

# Enable on boot
sudo systemctl enable digital-twin-ai

# Check status
sudo systemctl status digital-twin-ai

# View logs
sudo journalctl -u digital-twin-ai -f
```

### 5. Configure Nginx Reverse Proxy (Optional)

```bash
sudo apt install -y nginx

sudo nano /etc/nginx/sites-available/digital-twin-ai
```

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # WebSocket support
    location /api/messages/stream {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_read_timeout 86400;
    }
}
```

```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/digital-twin-ai /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# Restart Nginx
sudo systemctl restart nginx
```

### 6. Configure Firewall

```bash
# Allow HTTP/HTTPS
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Or allow specific port if not using Nginx
sudo ufw allow 8080/tcp

sudo ufw enable
```

---

## Environment Configuration for Server

Update `.env.local` for server deployment:

```bash
# Application Settings
ENVIRONMENT="production"
LOG_LEVEL="info"
APP_DATA_DIR="/var/lib/digital-twin-ai"

# Database
DATABASE_NAME="/var/lib/digital-twin-ai/digital_twin.db"

# Network
BIND_ADDRESS="0.0.0.0"
BIND_PORT="8080"
CORS_ALLOWED_ORIGINS="https://your-domain.com"

# Security
SECURITY_ENABLE_HTTPS="false"  # Let Nginx handle HTTPS
SECURITY_JWT_SECRET="your-secret-key-here"
SECURITY_SESSION_TIMEOUT="3600"

# ... rest of config
```

---

## Access Patterns

### 1. Local Network Access
```
http://192.168.1.100:8080
```

### 2. Public Access (with domain)
```
https://digital-twin.yourdomain.com
```

### 3. SSH Tunnel (for development)
```bash
ssh -L 8080:localhost:8080 user@server
# Then access: http://localhost:8080
```

---

## Comparison: Web Server vs Tauri Desktop

| Feature | Web Server | Tauri Desktop |
|---------|-----------|---------------|
| **Headless Server** | ✅ Yes | ❌ Requires X11 |
| **Multi-user** | ✅ Yes | ❌ Single user |
| **Resource Usage** | ✅ Lower | ❌ Higher |
| **Access** | 🌐 Browser | 🖥️ Native app |
| **Deployment** | ✅ Simple | ❌ Complex |
| **Latency** | ✅ Low (LAN) | ⚠️ Variable |
| **Offline Mode** | ❌ No | ✅ Yes |
| **Native Integration** | ❌ Limited | ✅ Full |

---

## Recommendation

**For Ubuntu Server (headless)**: Use the **Web Server mode** (Architecture 1)

**Reasons**:
1. No desktop environment needed
2. Accessible from any device
3. Better resource utilization
4. Easier deployment and updates
5. Support multiple users
6. Standard web security practices apply

**Core logic remains identical** - only the presentation layer (Tauri IPC → HTTP/WebSocket) changes.

---

## Hybrid Approach (Best of Both Worlds)

You can support BOTH modes with conditional compilation:

```toml
[features]
default = ["desktop"]
desktop = ["tauri"]
server = ["axum"]
```

```rust
#[cfg(feature = "desktop")]
fn main() {
    tauri::Builder::default()
        // Tauri app
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}

#[cfg(feature = "server")]
#[tokio::main]
async fn main() {
    // Web server app
    axum::Server::bind(&addr)
        .serve(app.into_make_service())
        .await
        .expect("Failed to start server");
}
```

Build for different targets:
```bash
# Desktop version
cargo build --release --features desktop

# Server version
cargo build --release --features server --no-default-features
```

This gives you maximum flexibility!
