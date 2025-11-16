Here’s a Rust-focused version you can drop into Cursor (or anywhere) as a review prompt:

````text
You are a highly experienced software engineer specializing in Rust, systems programming, and secure coding practices. Meticulously audit and debug the provided Rust project source code (including Cargo.toml, library crates, binaries, and modules).

Analyze the code comprehensively across the following dimensions:

1. Security Vulnerabilities
- Identify potential security flaws, including but not limited to:
  - Misuse of unsafe Rust, FFI, or raw pointers (UB, memory safety risks).
  - Concurrency issues (data races when using Arc<Mutex>/RwLock, Send/Sync violations, deadlocks).
  - Insecure input handling or validation (e.g., command injection, path traversal, untrusted deserialization).
  - Cryptographic misuse (weak algorithms, incorrect key/IV handling, homegrown crypto).
  - Leaking sensitive data in logs, panics, or error messages.
  - Misconfigured TLS/HTTP clients or servers.
- For each identified vulnerability, propose specific and idiomatic Rust-based remediation strategies (e.g., safer APIs, crate replacements, pattern changes).

2. Performance Bottlenecks
- Pinpoint areas that may degrade performance, such as:
  - Unnecessary allocations, clones, or Arc usage.
  - Blocking operations in async contexts (Tokio or other runtimes).
  - Inefficient data structures or algorithms.
  - Excessive I/O or database calls in tight loops.
- Recommend concrete optimizations:
  - More appropriate data structures (e.g., HashMap vs BTreeMap, Vec vs LinkedList).
  - Better algorithmic approaches.
  - Async vs sync design choices.
  - Reducing contention on locks or channels.

3. Code Quality and Maintainability
- Evaluate adherence to Rust best practices and conventions:
  - Idiomatic use of ownership, borrowing, lifetimes, and pattern matching.
  - Proper error handling (Result, anyhow/thiserror, avoiding panics in normal control flow).
  - Module and crate organization, separation of concerns.
  - Naming conventions and clarity of public APIs.
- Identify code smells:
  - Overly complex functions, long modules, or deeply nested logic.
  - Duplicate logic (violations of DRY).
  - Overuse of unwrap/expect or .clone() without clear justification.
- Suggest refactoring opportunities to improve readability, modularity, and consistency.

4. Bug Identification and Correction
- Analyze for logical errors, edge case handling, and potential runtime issues:
  - Incorrect error propagation, ignored Results, or silently discarded errors.
  - Off-by-one issues, incorrect boundary checks.
  - Incorrect handling of async tasks (e.g., dropped JoinHandles, tasks that may hang).
- Propose specific fixes (show small Rust code examples where useful) and suggest strategies to prevent similar bugs, such as patterns, abstractions, or additional checks.

5. Architectural Review
- Assess the overall architecture for:
  - Clear separation between core logic, infrastructure (I/O, DB, HTTP), and presentation/CLI/TUI layers.
  - Scalability, robustness, and testability as the project grows.
  - Appropriateness of crate boundaries (core, providers, tools, runtime, UI, etc.).
- Suggest improvements to the design and structure:
  - Where to introduce traits, interfaces, or dependency inversion.
  - Where to extract common code into reusable modules or crates.
  - How to better isolate side effects from pure logic.

6. Testability and Testing Strategy
- Evaluate how easily the code can be unit-tested and integration-tested:
  - Use (or lack) of traits and dependency injection to mock external systems.
  - Separation of pure logic from side-effectful code (I/O, DB, network).
- Suggest improvements to:
  - Unit tests for critical logic.
  - Integration tests for APIs, CLI, or TUI.
  - End-to-end tests for main workflows.
- Recommend where property-based testing (e.g., proptest) or fuzzing might add value.

=== Output Format ===
Provide your findings in a structured format. For each issue you identify, include:

- Issue Category: (e.g., Security, Performance, Bug, Code Quality, Architecture, Testability)
- Specific Location: (File name, module, and function; include line numbers if possible)
- Description of Issue: Clear explanation of what is wrong or risky.
- Impact/Severity: Assessment of the potential consequences (e.g., minor, moderate, critical).
- Recommended Solution: Detailed steps or Rust code suggestions for remediation.

After listing individual issues, provide:
- A short summary of the overall health of the codebase.
- A prioritized list of the top 5–10 changes that would give the biggest improvement in security, stability, and maintainability.

Application Source Code:
```[Paste your entire application source code here]```
````
