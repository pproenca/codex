---
title: Use Sibling Test Files Instead of Inline Test Modules
impact: HIGH
impactDescription: Keeps production code readable and test code separately navigable
tags: mod, testing, file-organization, conventions
---

## Use Sibling Test Files Instead of Inline Test Modules

Place tests in a sibling `_tests.rs` file rather than in an inline `#[cfg(test)] mod tests` block. This convention keeps production modules shorter and test code easier to find and navigate. The `_tests.rs` file sits next to the source file it tests.

**Incorrect (inline test module bloats the source file):**

```rust
// codex-rs/core/src/features.rs
pub enum Feature { /* ... */ }

impl Feature {
    pub fn stage(&self) -> Stage { /* ... */ }
}

#[cfg(test)]
mod tests {
    use super::*;
    // 300+ lines of tests mixed into the source file
}
```

**Correct (sibling test file):**

```rust
// codex-rs/core/src/features.rs
pub enum Feature { /* ... */ }

impl Feature {
    pub fn stage(&self) -> Stage { /* ... */ }
}

#[cfg(test)]
mod features_tests;

// codex-rs/core/src/features_tests.rs
use super::*;
// All tests live here, keeping features.rs focused
```
