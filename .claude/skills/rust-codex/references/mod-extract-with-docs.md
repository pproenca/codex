---
title: Move Tests and Docs When Extracting Modules
impact: HIGH
impactDescription: Keeps invariants and documentation close to the code that owns them
tags: mod, refactoring, documentation, test-colocation
---

## Move Tests and Docs When Extracting Modules

When extracting code from a large module into a new one, move the related tests and module/type documentation to the new file. Leaving orphaned docs or tests in the original file creates confusion about which module owns the invariants.

**Incorrect (tests and docs left behind after extraction):**

```rust
// codex-rs/core/src/session.rs — original file
/// Manages the lifecycle of a streaming connection.
/// Handles reconnection, backoff, and state recovery.
pub struct StreamManager { /* moved to stream_manager.rs */ }

#[cfg(test)]
mod stream_manager_tests;
// stream_manager_tests.rs still imports from session.rs
```

**Correct (docs and tests move with the code):**

```rust
// codex-rs/core/src/stream_manager.rs — new file
/// Manages the lifecycle of a streaming connection.
/// Handles reconnection, backoff, and state recovery.
pub struct StreamManager { /* ... */ }

#[cfg(test)]
mod stream_manager_tests;

// codex-rs/core/src/stream_manager_tests.rs
use super::*;
// All StreamManager tests live alongside the implementation
```
