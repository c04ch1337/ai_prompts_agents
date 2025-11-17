# Cursor Configuration Template for Rust Projects

## CLOUD AGENT SETTINGS

### Personal Configuration
```yaml
# .cursor/agent_config.yaml
base_environment: "ubuntu-latest"  # or "windows-latest"
sharing: "database"
pricing: "usage-based"
github_access: "verified"

runtime_configuration:
  install_script: |
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
    source $HOME/.cargo/env
    rustup component add rustfmt
    rustup component add clippy
    cargo install cargo-watch
    cargo install cargo-edit
    
  startup_script: |
    source $HOME/.cargo/env
    export RUST_BACKTRACE=1

secrets:
  - CARGO_REGISTRY_TOKEN
  - GITHUB_TOKEN
```

## RULES & MEMORIES

### CLAUDE.md (Project Context)
```markdown
# Rust Project Conventions

## Development Standards
- **Code Style**: Use rustfmt with default settings
- **Linting**: Enforce clippy pedantic rules
- **Testing**: Place unit tests in same file, integration tests in tests/
- **Documentation**: Document all public APIs with /// comments
- **Error Handling**: Use anyhow for applications, thiserror for libraries

## Project Structure
```
src/
├── main.rs      # Binary crate entry point
├── lib.rs       # Library crate entry point  
├── models/      # Data structures
├── services/    # Business logic
├── handlers/    # Request handlers (if web)
└── utils/       # Shared utilities
```

## Dependencies
- Prefer popular, well-maintained crates (check downloads, maintenance status)
- Use cargo-edit for dependency management: `cargo add`
- Keep dependencies minimal and well-documented

## Memory Conventions
- Important design decisions go in DECISIONS.md
- Configuration patterns go in CONFIGURATION.md
- Performance considerations in PERFORMANCE.md
```

### CLAUDE.local.md (Personal Preferences)
```markdown
# Developer Preferences

## Workflow
- Prefer iterative development with small, testable changes
- Write tests alongside feature development
- Use cargo watch for continuous testing: `cargo watch -x test`
- Format code before committing: `cargo fmt`

## Debugging
- Always enable RUST_BACKTRACE=1 during development
- Use `dbg!()` macro for quick debugging
- Prefer logging over println for production code

## Code Review Focus
- Memory safety and ownership patterns
- Error handling completeness
- Performance implications of abstractions
- Test coverage for edge cases
```

### Saved Memories Setup
```bash
# Create memories directory structure
mkdir -p .cursor/memories
touch .cursor/memories/architecture_decisions.md
touch .cursor/memories/performance_notes.md
touch .cursor/memories/debugging_patterns.md
```

## RULES CONFIGURATION

### User Rules (.cursor/rules/user_rules.md)
```markdown
# Master Prompt for Cursor Agents

## Primary Directives
1. **Rust Expertise**: Always apply Rust best practices and safety principles
2. **Iterative Development**: Break down complex tasks into smaller, testable steps
3. **Context Awareness**: Consider existing project structure and conventions
4. **Error Handling**: Ensure robust error handling in all code suggestions
5. **Performance Conscious**: Suggest efficient, zero-cost abstractions when possible

## Communication Style
- Be concise but thorough in explanations
- Provide reasoning for architectural decisions
- Include examples when introducing new patterns
- Highlight potential pitfalls and alternatives

## Code Quality Standards
- Always suggest accompanying tests
- Prefer readable code over clever optimizations
- Ensure proper documentation for public APIs
- Follow established project patterns
```

### Project Rules (.cursor/rules/project_rules.md)
```markdown
# Rust Project Conventions

## Code Organization
- Module structure should mirror the domain
- One responsibility per module
- Use `mod.rs` files for module declarations
- Keep binary and library crates separated

## Dependency Management
- Audit dependencies regularly with `cargo audit`
- Pin important dependencies with = version
- Use workspace dependencies for monorepos

## Testing Strategy
- Unit tests in the same file as code
- Integration tests in tests/ directory
- Use `#[cfg(test)]` for test-only code
- Mock external dependencies in tests
```

## COMMANDS SETUP

### Project Commands (.cursor/commands/)
```bash
# Create command directory
mkdir -p .cursor/commands
```

#### .cursor/commands/test_watch.sh
```bash
#!/bin/bash
# Continuous testing with cargo-watch
cargo watch -x "test -- --nocapture"
```

#### .cursor/commands/format_check.sh
```bash
#!/bin/bash
# Check formatting and linting
cargo fmt -- --check
cargo clippy -- -D warnings
```

#### .cursor/commands/debug_build.sh
```bash
#!/bin/bash
# Debug build with full symbols
RUSTFLAGS="-g" cargo build
```

### User Commands (~/.cursor/commands/)
```bash
# Personal commands directory
mkdir -p ~/.cursor/commands
```

#### ~/.cursor/commands/new_rust_module.sh
```bash
#!/bin/bash
# Create new Rust module with template
if [ -z "$1" ]; then
    echo "Usage: new_rust_module <module_name>"
    exit 1
fi

MODULE_NAME=$1
cat > "src/${MODULE_NAME}.rs" << EOF
//! ${MODULE_NAME} module

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_example() {
        assert_eq!(2 + 2, 4);
    }
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
  - "**/node_modules/**"

priority_files:
  - "Cargo.toml"
  - "src/main.rs"
  - "src/lib.rs"
  - "README.md"
```

### Documentation Setup
```bash
# Add project documentation
mkdir -p docs
touch docs/ARCHITECTURE.md
touch docs/API_GUIDE.md
touch docs/DEVELOPMENT.md

# Add to Cargo.toml for documentation
echo '[package]' >> Cargo.toml
echo 'documentation = "https://docs.rs/your-crate"' >> Cargo.toml
```

### Workspace Configuration
```toml
# Cargo.toml workspace configuration (if applicable)
[workspace]
members = ["crates/*"]
resolver = "2"

[workspace.dependencies]
# Common dependencies for workspace
```

This template provides a comprehensive setup for Rust development in Cursor with cloud agents, proper memory management, Rust-specific rules, and useful commands for an efficient workflow.
