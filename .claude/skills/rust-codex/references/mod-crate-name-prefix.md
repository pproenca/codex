---
title: Prefix All Crate Names with codex-
impact: HIGH
impactDescription: Ensures consistent namespace and prevents crate name collisions
tags: mod, crate-naming, workspace, conventions
---

## Prefix All Crate Names with codex-

All crate names in the `codex-rs` workspace must be prefixed with `codex-`. For example, the `core` folder's crate is named `codex-core`, and `tui` becomes `codex-tui`. This applies to both library and binary crates.

**Incorrect (missing codex- prefix):**

```toml
# codex-rs/core/Cargo.toml
[package]
name = "core"

# codex-rs/tui/Cargo.toml
[package]
name = "tui"
```

**Correct (codex- prefix on all crates):**

```toml
# codex-rs/core/Cargo.toml
[package]
name = "codex-core"

# codex-rs/tui/Cargo.toml
[package]
name = "codex-tui"
```
