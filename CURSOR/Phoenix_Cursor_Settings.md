# Cursor Configuration Template for Async Rust Workflows

This configuration is designed for Rust projects emphasizing asynchronous workflows, such as those using Tokio for concurrency, agentic systems, or I/O-bound applications like web servers, databases, or networked agents. It builds on standard Cursor setups but prioritizes async best practices, including structured concurrency, error propagation, and performance optimizations. Use this to enhance productivity in Cursor AI editor for async Rust development, drawing from established rules and workflows.

## CLOUD AGENT SETTINGS
### Personal Configuration
```yaml
# .cursor/agent_config.yaml
base_environment: "ubuntu-latest" # Optimal for Rust async runtimes like Tokio
sharing: "database" # Persist agent state for long-running async simulations
pricing: "usage-based" # Efficient for iterative async debugging
github_access: "verified" # For integrating with async crates or repos
runtime_configuration:
  install_script: |
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
    source $HOME/.cargo/env
    rustup component add rustfmt
    rustup component add clippy
    rustup component add rls
    cargo install cargo-watch
    cargo install cargo-edit
    cargo install cargo-audit
    cargo install cargo-nextest # Faster tests for async code
    cargo install tokio-console # For runtime metrics in async workflows
  startup_script: |
    source $HOME/.cargo/env
    export RUST_BACKTRACE=full # Detailed traces for async panics
    export RUST_LOG=trace # High verbosity for async task logging
    export TOKIO_CONSOLE_BIND=127.0.0.1:6669 # Enable Tokio console for monitoring
secrets:
  - CARGO_REGISTRY_TOKEN
  - GITHUB_TOKEN
  - DATABASE_URL # For async DB testing, e.g., with sqlx
```

## RULES & MEMORIES
### CLAUDE.md (Project Context)
```markdown
# Async Rust Workflow Conventions
## Development Standards
- **Code Style**: Use rustfmt; enforce async idioms like `async fn` and `.await`.
- **Linting**: Clippy pedantic with focus on async-safety (e.g., no_blocking, must_use_future).
- **Testing**: Inline unit tests with `#[tokio::test]`; integration tests in tests/ for full async flows.
- **Documentation**: /// comments with async examples (e.g., task spawning, select! usage).
- **Error Handling**: anyhow for apps, thiserror for libs; propagate with ? in async chains.
- **Concurrency**: Use Tokio for runtime; prefer structured concurrency with scopes.
## Project Structure
```
src/
├── main.rs # Async entry point with tokio::main
├── lib.rs # Async traits and utilities
├── async_ops/ # Async functions, tasks, and channels
├── concurrency/ # Mutex, RwLock, broadcast implementations
├── integrations/ # Async HTTP (reqwest), DB (sqlx), gRPC (tonic)
├── models/ # Async-safe data structures
├── services/ # Business logic with async workflows
└── utils/ # Logging (tracing), serialization (serde)
```
## Dependencies
- Core async: tokio = { version = "1", features = ["full"] }
- HTTP: reqwest = { version = "0.12", features = ["json"] }
- DB: sqlx = { version = "0.8", features = ["runtime-tokio", "postgres"] }
- Others: tracing, serde, thiserror, anyhow
- Audit regularly: `cargo audit`; pin versions for stability.
## Memory Conventions
- Async patterns (e.g., timeouts, retries) in ASYNC_DECISIONS.md
- Performance metrics (e.g., task contention) in PERFORMANCE.md
- Error patterns in async flows in ERRORS.md
```

### CLAUDE.local.md (Personal Preferences)
```markdown
# Preferences for Async Rust Workflows
## Workflow
- Iterative: Build async components in isolation, then integrate with select! or join!.
- TDD: Write failing async tests first; use AI to implement and refactor.
- Continuous: `cargo watch -x 'nextest run'` for async test feedback.
- Format/Lint: `cargo fmt` and `cargo clippy --all-features` pre-commit.
## Debugging
- RUST_BACKTRACE=full and tracing::trace! for async task inspection.
- Use tokio-console for runtime visualization; dbg! for quick futures checks.
- Simulate loads: Spawn multiple tasks to test concurrency edges.
## Code Review Focus
- Async correctness: No blocking in async, proper cancellation.
- Ownership in shared state: Minimize locks, use channels.
- Performance: Yield in loops, bounded channels for backpressure.
- Tests: Cover timeouts, errors, and multi-task interactions.
```

### Saved Memories Setup
```bash
# Create memories directory
mkdir -p .cursor/memories
touch .cursor/memories/async_decisions.md  # e.g., Structured concurrency rationale
touch .cursor/memories/performance_notes.md  # e.g., Optimizing select! branches
touch .cursor/memories/debugging_patterns.md  # e.g., Tracing async deadlocks
touch .cursor/memories/error_patterns.md  # e.g., Custom errors in async chains
```

## RULES CONFIGURATION
### User Rules (.cursor/rules/user_rules.md)
```markdown
# Master Prompt for Async Rust in Cursor
## Primary Directives
1. **Rust Async Expertise**: Apply Tokio best practices; use async fn, spawn, select!.
2. **Iterative Async Development**: Break tasks into spawnable units; test concurrently.
3. **Context Awareness**: Align with async ecosystem (Tokio, reqwest, sqlx).
4. **Error Handling**: Propagate with ?; use custom errors for async ops.
5. **Performance Conscious**: Minimize awaits; use yield_now for cooperation.
## Communication Style
- Explain async flows: "Use select! to handle cancellation in this workflow."
- Reason choices: "Prefer mpsc for backpressure in producer-consumer async patterns."
- Pitfalls: "Avoid Mutex in hot paths to prevent contention."
## Code Quality Standards
- Suggest #[tokio::test] for all async suggestions.
- Readable async: Group awaits logically; document task lifecycles.
- Document with async examples.
- Follow conventions: Modular async ops, safe concurrency.
```

### Project Rules (.cursor/rules/project_rules.md)
```markdown
# Async Rust Project Conventions
## Code Organization
- Modules by async domain: e.g., async_ops/mod.rs for submodules.
- Single responsibility: e.g., concurrency/channels.rs for mpsc/broadcast.
- Separate bin/lib for reusable async components.
## Dependency Management
- Audit: `cargo audit --ignore unknown`; focus on async crates.
- Pin: tokio = "1"; use workspaces for multi-crate async setups.
## Testing Strategy
- Inline units: Test async fns with tokio::test.
- Integrations: Simulate full workflows with mocks (e.g., mockito for HTTP).
- Cover: Timeouts (tokio::time::pause), edges (deadlocks, races).
- Benchmarks: criterion for async performance.
```

## COMMANDS SETUP
### Project Commands (.cursor/commands/)
```bash
mkdir -p .cursor/commands
```

#### .cursor/commands/test_watch.sh
```bash
#!/bin/bash
# Continuous async testing
cargo watch -x "nextest run --all-features"
```

#### .cursor/commands/format_check.sh
```bash
#!/bin/bash
# Format, lint, audit for async safety
cargo fmt -- --check
cargo clippy --all-features -- -D warnings -D clippy::async_yields_async
cargo audit
```

#### .cursor/commands/debug_async.sh
```bash
#!/bin/bash
# Run with Tokio console enabled
RUSTFLAGS="-g" RUST_LOG=trace cargo run --features debug
tokio-console
```

#### .cursor/commands/simulate_load.sh  # For async workflow testing
```bash
#!/bin/bash
# Simulate concurrent tasks
cargo run -- --tasks 100 --timeout 10s
```

### User Commands (~/.cursor/commands/)
```bash
mkdir -p ~/.cursor/commands
```

#### ~/.cursor/commands/new_async_module.sh
```bash
#!/bin/bash
# Create async Rust module template
if [ -z "$1" ]; then
    echo "Usage: new_async_module <module_name>"
    exit 1
fi
MODULE_NAME=$1
cat > "src/${MODULE_NAME}.rs" << EOF
//! Async ${MODULE_NAME} module
use anyhow::Result;
use tokio::sync::mpsc;

pub async fn ${MODULE_NAME}_task() -> Result<()> {
    // Async logic here
    Ok(())
}

#[cfg(test)]
#[tokio::test]
async fn test_${MODULE_NAME}_task() {
    assert!( ${MODULE_NAME}_task().await.is_ok() );
}
EOF
```

## INDEXING & DOCUMENTATION
### Codebase Indexing Configuration
```yaml
# .cursor/indexing.yaml
file_extensions:
  - .rs
  - .toml
  - .md
ignore_patterns:
  - "**/target/**"
  - "**/.git/**"
priority_files:
  - "Cargo.toml"
  - "src/main.rs"
  - "src/lib.rs"
  - "README.md"
  - "ASYNC_DECISIONS.md"
```

### Documentation Setup
```bash
mkdir -p docs
touch docs/ASYNC_ARCHITECTURE.md  # Tokio patterns overview
touch docs/API_GUIDE.md  # Async function usage
touch docs/DEVELOPMENT.md  # Setup for Tokio and tracing
# Cargo.toml doc
echo '[package]' >> Cargo.toml
echo 'documentation = "https://docs.rs/async-project"' >> Cargo.toml
```

### Workspace Configuration
```toml
# Cargo.toml for async multi-crate
[workspace]
members = ["crates/*"]
resolver = "2"
[workspace.dependencies]
tokio = { version = "1", features = ["full"] }
reqwest = { version = "0.12", features = ["json"] }
sqlx = { version = "0.8", features = ["runtime-tokio", "postgres"] }
tracing = "0.1"
serde = { version = "1", features = ["derive"] }
anyhow = "1"
thiserror = "1"
```

This template optimizes Cursor for async Rust, ensuring AI assistance aligns with best practices for concurrency and performance. Integrate it by placing files in your project, and use the rules to guide AI interactions for efficient workflows.
