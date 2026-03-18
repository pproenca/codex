---
title: Use Scoped just fix for Clippy Linting
impact: LOW-MEDIUM
impactDescription: Catches lint issues locally, avoids slow workspace-wide Clippy builds
tags: ci, clippy, linting, just-command
---

## Use Scoped just fix for Clippy Linting

Before finalizing a large change, run `just fix -p <project>` in the `codex-rs` directory to fix linter issues. Scope with `-p` to the changed crate (e.g., `just fix -p codex-core`) to avoid slow workspace-wide Clippy builds. Only run `just fix` without `-p` if you changed shared crates.

**Incorrect (workspace-wide Clippy on a single-crate change):**

```bash
# Changed only codex-rs/tui/src/app.rs
just fix
# Runs Clippy on ALL crates — takes 5-10 minutes
```

**Correct (scoped Clippy to the changed crate):**

```bash
# Changed only codex-rs/tui/src/app.rs
just fix -p codex-tui
# Runs Clippy only on codex-tui — takes 30-60 seconds
```
