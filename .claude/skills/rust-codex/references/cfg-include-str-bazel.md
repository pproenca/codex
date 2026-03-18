---
title: Update BUILD.bazel for Compile-Time File Access
impact: MEDIUM
impactDescription: Prevents Bazel build failures when Cargo passes but Bazel does not
tags: cfg, bazel, include-str, build-system
---

## Update BUILD.bazel for Compile-Time File Access

Bazel does not automatically expose source-tree files to compile-time Rust file access. When adding `include_str!`, `include_bytes!`, `sqlx::migrate!`, or similar build-time reads, update the crate's `BUILD.bazel` (`compile_data`, `build_script_data`, or test data). Cargo builds will pass without this, but Bazel will fail.

**Incorrect (include_str works in Cargo but fails in Bazel):**

```rust
// codex-rs/core/src/prompts.rs
const SYSTEM_PROMPT: &str = include_str!("../prompts/system.txt");
// BUILD.bazel not updated — Bazel build fails
```

**Correct (BUILD.bazel updated with compile_data):**

```rust
// codex-rs/core/src/prompts.rs
const SYSTEM_PROMPT: &str = include_str!("../prompts/system.txt");

// BUILD.bazel for codex-core:
// rust_library(
//     name = "codex-core",
//     compile_data = ["prompts/system.txt"],
//     ...
// )
```
