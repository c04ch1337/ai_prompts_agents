**Prompt Title**: **Audit and Refactor a Monolithic Rust Application for Modularity and Performance Optimization**

---

**Context**:
I have a monolithic Rust application that is growing large and difficult to maintain. It is currently a **single binary** with several large modules that include both core logic and infrastructure concerns. I need to **reduce the overall size** of the application, **increase maintainability**, and **improve performance** by refactoring the codebase. I also want to modularize the application and potentially split it into **smaller crates**.

Please **audit the codebase**, and **provide a detailed refactor plan** to address the following:

1. **Code Modularization and Separation of Concerns**:

   * Break the application into smaller, logically separated modules or crates using **Cargo workspaces**.
   * Ensure that the **core logic** is separated from **infrastructure concerns** (e.g., database, file I/O, network).
   * Identify areas where different **modules** or **functionalities** can be isolated into separate crates.

2. **Identify Unnecessary Dependencies**:

   * Review the `Cargo.toml` for **large or unused dependencies** that contribute to the application’s size and slow down build times.
   * Suggest **lighter or more efficient alternatives** if applicable.

3. **Performance Optimizations**:

   * **Reduce bloat**: Identify sections of the codebase that are **over-abstracted** or unnecessarily complex and suggest simplifications.
   * Review the code for any **unnecessary `.clone()` calls** and **shared ownership** patterns like `Arc<Mutex>`. Recommend more efficient alternatives.
   * Optimize data structures used in hot paths for better memory and performance efficiency.

4. **Refactor Blocking Operations in Async Contexts**:

   * Identify any **blocking operations** (e.g., I/O operations) that exist in `async` functions and suggest converting them to **non-blocking async equivalents**.
   * If some blocking operations cannot be converted, suggest how to **offload them to a separate thread pool** using `tokio::task::block_in_place`.

5. **Test Coverage**:

   * Recommend a **testing strategy** for this refactor. How can I ensure proper **unit testing** and **integration testing** for core components and interactions between the refactored crates?
   * Suggest ways to write **end-to-end tests** that validate the workflows of the application.

6. **Overall Code Review**:

   * Audit the project for **duplicate logic** and areas where code could be refactored to avoid repetition.
   * Suggest breaking up **large functions** into smaller, more manageable ones.
   * Ensure adherence to **Rust best practices** for error handling, async programming, and concurrency.

7. **Build Optimization**:

   * Review the project’s **build times** and suggest ways to **speed up compilation** (e.g., through parallel builds or other optimizations).
   * Identify areas where **cargo features** could be leveraged to reduce unnecessary compilation during development.

---

**Expected Output**:

* A **detailed plan** for refactoring the monolithic application into smaller, more modular parts, including:

  * **Crate structure** (with example file structure and dependencies).
  * **Code snippets** showing specific optimizations (e.g., replacing `Arc<Mutex>` with `RwLock`, using async I/O, removing unnecessary clones).
  * **Recommendations** for **removing dependencies** and improving performance.
  * **Testing strategy**: Best practices for writing tests after the modularization.
  * Suggested **performance benchmarks** or tools to validate improvements (e.g., `cargo bloat`, `cargo flamegraph`).
  * A high-level roadmap for refactoring the application gradually.

---

**Additional Notes**:

* The project is large (~10 GB before compilation) and is currently a **monolithic** single binary with multiple modules.
* I need to reduce the **binary size** without sacrificing functionality or performance.
* Focus on improving **maintainability**, **testability**, and reducing the **complexity** of the code.

---

Let me know if you'd like to refine this further or add any additional requirements!
