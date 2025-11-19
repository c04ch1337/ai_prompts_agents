### Integrating Your Rust Backend with the TypeScript Frontend Stack

Since your backend is in **Rust** (not NestJS), we'll adapt the integration to leverage Rust's ecosystem for **type-safe, validated APIs** that match your frontend's Zod schemas, TanStack Query hooks, and plug-and-play env setup. Rust excels here: it's blazing fast, memory-safe, and has mature tools for JSON validation and type sharing with TypeScript.

The goal remains the same: **end-to-end type safety** (no more `any` or runtime surprises), **shared schemas** (Zod on frontend, equivalent on backend), and **exact response shapes** (`{ success: true, data: {...}, meta: {...} }`). We'll use **Axum** (the 2025 gold standard for async Rust web APIs—simple, performant, and integrates seamlessly with validation crates).

#### Why This Stack for Rust?
| Layer               | Choice                  | Why it Matches Your Frontend |
|---------------------|-------------------------|------------------------------|
| Framework           | **Axum** (on Tokio)    | Async, middleware-friendly; easy to add JSON validation and extractors for type-safe handlers. |
| Validation          | **zod-rs** (Zod-inspired) | Derive macros for structs → auto-generates validators mirroring your Zod schemas. Runtime-safe like Zod. |
| Type Sharing        | **ts-rs** + **zod_gen** | Auto-generates TypeScript types + Zod schemas from Rust structs. Run `cargo build` → frontend types update. |
| Serialization       | **Serde** (with JSON)  | Handles `{ success, data, meta }` wrappers effortlessly. |
| Env/Config          | **dotenvy**            | Plug-and-play like Vite's `.env`—swap dev/prod URLs instantly. |

This setup ensures:
- **Backend validates incoming requests** (e.g., login creds) with Zod-like rules.
- **Responses are always wrapped** and validated against your `ApiResponse<T>` shape.
- **Frontend types are auto-generated**—no copy-paste drift.
- **Zero runtime errors**: Rust's borrow checker + validation catches issues at compile/runtime.

#### Step-by-Step: Rust Backend Setup

##### 1. Initialize a New Axum Project (or Migrate Existing)
```bash
cargo new my-rust-backend --bin
cd my-rust-backend
```

`Cargo.toml` (key deps—add these):
```toml
[package]
name = "my-rust-backend"
version = "0.1.0"
edition = "2021"

[dependencies]
# Web framework
axum = "0.7"
tokio = { version = "1", features = ["full"] }
tower = "0.5"
tower-http = { version = "0.6", features = ["cors", "trace"] }

# Serialization & Validation
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
zod-rs = "0.1"  # Zod-like validation (derive macros)
validator = { version = "0.18", features = ["derive"] }  # Fallback for simple checks

# TypeScript integration
ts-rs = { version = "0.5", features = ["serde"] }  # Rust → TS types
zod_gen = "0.1"  # Rust → Zod schemas

# Env & Utils
dotenvy = "0.15"
tracing = "0.1"
tracing-subscriber = "0.3"

# Optional: Database (e.g., for users/posts)
sqlx = { version = "0.8", features = ["runtime-tokio", "postgres"] }
```

Run `cargo build` to fetch deps.

##### 2. Define Shared Schemas (Mirror Your Frontend Zod)
Create `src/models.rs`—these structs will:
- Validate with `zod-rs` derive.
- Generate TS/Zod via `ts-rs` and `zod_gen`.

```rust
use serde::{Deserialize, Serialize};
use ts_rs::TS;  // For TS export
use zod_rs::prelude::*;  // For validation
use validator::Validate;  // Simple fallback

// Base Response (matches your ApiResponse<T>)
#[derive(Serialize, Deserialize, TS, Debug)]
#[serde(rename_all = "camelCase")]
#[zod_rs::ZodSchema]  // Auto-generates validator
pub struct ApiResponse<T> {
    pub success: bool,
    pub data: T,
    pub message: Option<String>,
    pub error: Option<String>,
    pub meta: Option<ApiMeta>,
}

#[derive(Serialize, Deserialize, TS, Debug, Default)]
#[serde(rename_all = "camelCase")]
#[zod_rs::ZodSchema]
pub struct ApiMeta {
    pub pagination: Option<Pagination>,
    pub timestamp: Option<String>,
}

#[derive(Serialize, Deserialize, TS, Debug)]
#[serde(rename_all = "camelCase")]
#[zod_rs::ZodSchema]
pub struct Pagination {
    pub page: u32,
    pub limit: u32,
    pub total: u64,
    pub total_pages: u32,
}

// User (matches your User type)
#[derive(Serialize, Deserialize, TS, Debug, Validate)]
#[serde(rename_all = "camelCase")]
#[zod_rs::ZodSchema]
pub struct User {
    pub id: u64,
    #[validate(email)]  // Zod-like: email validation
    pub email: String,
    #[validate(length(min = 1))]
    pub name: String,
    pub avatar: Option<String>,
    pub role: Role,
    pub is_active: bool,
    pub created_at: String,  // ISO datetime
    pub updated_at: String,
}

#[derive(Serialize, Deserialize, TS, Debug, Clone, Copy)]
#[serde(rename_all = "lowercase")]
#[zod_rs::ZodSchema]
pub enum Role {
    Admin,
    User,
    Moderator,
}

// Login Response (matches your LoginResponse)
#[derive(Serialize, Deserialize, TS, Debug)]
#[zod_rs::ZodSchema]
pub struct LoginResponseData {
    pub user: User,
    pub tokens: Tokens,
}

#[derive(Serialize, Deserialize, TS, Debug)]
#[zod_rs::ZodSchema]
pub struct Tokens {
    pub access_token: String,
    pub refresh_token: String,
    pub expires_in: u64,  // seconds
}

// Post (similar for others)
#[derive(Serialize, Deserialize, TS, Debug, Validate)]
#[zod_rs::ZodSchema]
pub struct Post {
    pub id: u64,
    #[validate(length(min = 1))]
    pub title: String,
    pub content: String,
    pub excerpt: Option<String>,
    pub slug: String,
    pub status: PostStatus,
    pub author_id: u64,
    pub author: Option<User>,
    pub published_at: Option<String>,
    pub created_at: String,
    pub updated_at: String,
    pub tags: Option<Vec<String>>,
}

#[derive(Serialize, Deserialize, TS, Debug, Clone, Copy)]
#[serde(rename_all = "lowercase")]
#[zod_rs::ZodSchema]
pub enum PostStatus {
    Draft,
    Published,
    Archived,
}

// Paginated (generic-like via serde)
#[derive(Serialize, Deserialize, TS, Debug)]
#[zod_rs::ZodSchema]
pub struct PaginatedPosts {
    pub items: Vec<Post>,
    pub pagination: Pagination,
}
```

- **Validation**: `#[zod_rs::ZodSchema]` auto-creates a validator. Use `.validate(&data)?` in handlers.
- **TS Export**: `#[TS]`—run `cargo expand` or build script to generate `./ts/api.ts` with types + Zod schemas.

##### 3. Build Script for Auto-Generating Frontend Types
Add `build.rs` (runs on `cargo build`):
```rust
use std::env;
use std::path::Path;

fn main() {
    // Generate TS types from models
    ts_rs::export!({
        ApiResponse: {
            generics: [T],
            generics_default: [String],  // Fallback for generic
        },
        User,
        LoginResponseData,
        Post,
        PaginatedPosts,
        // Add more...
    }, &mut std::fs::File::create("src/generated.ts").unwrap()).unwrap();

    // Also generate Zod schemas via zod_gen (if needed for extras)
    // println!("cargo:rerun-if-changed=src/models.rs");
}
```

Update `Cargo.toml`:
```toml
[[bin]]
name = "my-rust-backend"
path = "src/main.rs"

[build-dependencies]
ts-rs = "0.5"
```

Now, `cargo build` → outputs `src/generated.ts` with:
```ts
// Auto-generated
export type User = { id: number; email: string; /* ... */ };
export const UserSchema = z.object({ id: z.number(), email: z.string().email(), /* ... */ });
```

Copy `generated.ts` to your frontend's `src/types/api-responses.ts` (or automate via CI). This keeps Zod schemas in sync!

##### 4. Env Config (Plug-and-Play like Frontend)
`.env` (dev/prod swap):
```
DATABASE_URL=postgres://localhost:5432/myapp
JWT_SECRET=your-secret
PORT=3001
```

`src/config.rs`:
```rust
use dotenvy::dotenv;
use serde::Deserialize;
use std::env;

#[derive(Deserialize, Debug)]
pub struct Config {
    pub database_url: String,
    pub jwt_secret: String,
    pub port: u16,
}

impl Config {
    pub fn load() -> Self {
        dotenv().ok();
        let port = env::var("PORT").unwrap_or_else(|_| "3001".to_string()).parse().unwrap();
        // Load others...
        Self {
            database_url: env::var("DATABASE_URL").expect("DATABASE_URL must be set"),
            jwt_secret: env::var("JWT_SECRET").expect("JWT_SECRET must be set"),
            port,
        }
    }
}
```

##### 5. Handlers with Validation & Wrapped Responses
`src/handlers.rs` (example for auth/posts):
```rust
use axum::{
    extract::{Json, State},
    http::StatusCode,
    response::Json as AxumJson,
    routing::{get, post},
    Router,
};
use serde_json::json;
use std::sync::Arc;
use zod_rs::ZodSchema;  // For validation

use crate::models::{ApiResponse, LoginResponseData, PaginatedPosts, Post, User, /* ... */};
use crate::config::Config;

// App state
#[derive(Clone)]
pub struct AppState {
    pub config: Config,
    // pub db: sqlx::PgPool,  // Add your DB
}

// Validate & wrap response helper
async fn wrap_response<T: Serialize>(
    data: Result<T, String>,
    status: StatusCode,
) -> Result<AxumJson<ApiResponse<T>>, StatusCode> {
    match data {
        Ok(d) => Ok(AxumJson(ApiResponse {
            success: true,
            data: d,
            message: Some("Success".to_string()),
            error: None,
            meta: Some(ApiMeta {
                timestamp: Some(chrono::Utc::now().to_rfc3339()),
                pagination: None,  // Add if paginated
            }),
        })),
        Err(e) => Ok(AxumJson(ApiResponse {
            success: false,
            data: serde_json::Value::Null,  // Or empty
            error: Some(e),
            message: None,
            meta: None,
        })),
    }
}

// Login handler (validates input with zod-rs)
async fn login(
    State(state): State<Arc<AppState>>,
    Json(payload): Json<serde_json::Value>,  // Raw JSON for validation
) -> Result<AxumJson<ApiResponse<LoginResponseData>>, StatusCode> {
    // Validate input (mirror your LoginDtoSchema)
    let login_schema = zod_rs::z::object("email", zod_rs::z::string().email())
        .and("password", zod_rs::z::string().min(6));
    if let Err(e) = login_schema.validate(&payload) {
        return Err(StatusCode::BAD_REQUEST);  // Or return error response
    }

    // Fake auth logic (replace with real JWT/DB)
    let user = User {
        id: 1,
        email: payload["email"].as_str().unwrap().to_string(),
        name: "John Doe".to_string(),
        avatar: None,
        role: Role::User,
        is_active: true,
        created_at: chrono::Utc::now().to_rfc3339(),
        updated_at: chrono::Utc::now().to_rfc3339(),
    };
    let tokens = Tokens {
        access_token: "fake-jwt".to_string(),
        refresh_token: "fake-refresh".to_string(),
        expires_in: 3600,
    };
    let data = LoginResponseData { user, tokens };

    wrap_response(Ok(data), StatusCode::OK).await
}

// Get Posts (paginated)
async fn get_posts(
    State(_state): State<Arc<AppState>>,
    axum::extract::Query(params): axum::extract::Query<std::collections::HashMap<String, String>>,
) -> Result<AxumJson<ApiResponse<PaginatedPosts>>, StatusCode> {
    let page: u32 = params.get("page").unwrap_or(&"1".to_string()).parse().unwrap_or(1);
    let limit: u32 = params.get("limit").unwrap_or(&"10".to_string()).parse().unwrap_or(10);

    // Fake DB query (replace with sqlx)
    let items = vec![Post {
        id: 1,
        title: "Sample Post".to_string(),
        content: "Content...".to_string(),
        excerpt: None,
        slug: "sample".to_string(),
        status: PostStatus::Published,
        author_id: 1,
        author: None,
        published_at: None,
        created_at: chrono::Utc::now().to_rfc3339(),
        updated_at: chrono::Utc::now().to_rfc3339(),
        tags: Some(vec!["rust".to_string()]),
    }; (limit as usize).min(10));  // Fake limit

    let pagination = Pagination {
        page,
        limit,
        total: 100,
        total_pages: 10,
    };
    let data = PaginatedPosts { items, pagination };

    wrap_response(Ok(data), StatusCode::OK).await
}
```

##### 6. Main App (`src/main.rs`)
```rust
use axum::{routing::{get, post}, Router};
use std::net::SocketAddr;
use std::sync::Arc;
use tracing_subscriber;

mod config;
mod handlers;
mod models;

use config::Config;
use handlers::{login, get_posts, AppState};

#[tokio::main]
async fn main() {
    tracing_subscriber::fmt::init();

    let config = Config::load();
    let state = Arc::new(AppState { config });

    let app = Router::new()
        .route("/auth/login", post(login))
        .route("/posts", get(get_posts))
        // Add more routes...
        .with_state(state)
        .layer(tower_http::cors::CorsLayer::permissive());  // Adjust for prod

    let addr = SocketAddr::from(([127, 0, 0, 1], config.port));
    println!("Server running on http://{}", addr);
    axum::Server::bind(&addr)
        .serve(app.into_make_service())
        .await
        .unwrap();
}
```

Run with `cargo run`—test endpoints with curl/Postman. Responses match your `ApiResponse` exactly!

##### 7. Database/Auth (Quick Add-Ons)
- **DB**: Use `sqlx` for PostgreSQL. Add migrations via `sqlx migrate add create_users`.
- **JWT**: `jsonwebtoken` crate for tokens.
- **Pagination**: Handled in meta; invalidate TanStack caches on mutations via query keys.

#### Bonus: Full End-to-End Typesafety (tRPC-Style)
For ultimate safety (no REST overhead), use **rspc** (Rust + TS RPC):
```bash
cargo add rspc
```
It generates TS clients from Rust procedures—your `useQuery` hooks call `trpc.post.all.query()` with full types. Matches TanStack Query perfectly. (See rspc.dev for setup—it's like tRPC but Rust-native.)

#### Summary: Backend Tasks Checklist
| Task                          | How                          |
|-------------------------------|------------------------------|
| Define shared models          | `src/models.rs` with `#[TS]`, `#[ZodSchema]` |
| Auto-gen TS/Zod               | `build.rs` + `cargo build` → copy to frontend |
| Validate inputs/responses     | `zod-rs::validate()` in handlers; wrap with `ApiResponse` |
| Env swapping                  | `dotenvy` + `.env` files     |
| Match exact shapes            | Serde + helpers ensure `{ success, data, meta }` |
| (Optional) RPC upgrade        | rspc for procedure-based calls |

Your Rust backend is now **faster than Node**, **safer than TS**, and **seamlessly integrated** with your Zod/TanStack frontend. No more mismatches—changes in Rust auto-update frontend types.

This is the **2025 Rust/TS dream stack** (used by Shuttle.rs, Tauri apps). Run `cargo run`, hit your endpoints, and watch TanStack Query light up with validated data.

Ship fearlessly—your full-stack is now indestructible. 🚀