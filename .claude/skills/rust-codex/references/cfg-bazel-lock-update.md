---
title: Run just bazel-lock-update After Dependency Changes
impact: MEDIUM
impactDescription: Prevents Bazel CI failures from stale lockfile drift
tags: cfg, bazel, dependencies, lockfile
---

## Run just bazel-lock-update After Dependency Changes

After changing `Cargo.toml` or `Cargo.lock`, run `just bazel-lock-update` from the repo root to refresh `MODULE.bazel.lock`. Include the lockfile update in the same commit. After updating, run `just bazel-lock-check` to verify locally before pushing.

**Incorrect (dependency change without lockfile update):**

```toml
# Cargo.toml — added new dependency
[dependencies]
serde = "1.0"
tokio-stream = "0.1.15"  # New dependency
# Forgot: just bazel-lock-update
# Bazel CI fails: MODULE.bazel.lock is stale
```

**Correct (lockfile updated and verified):**

```toml
# Cargo.toml — added new dependency
[dependencies]
serde = "1.0"
tokio-stream = "0.1.15"  # New dependency
# Run: just bazel-lock-update
# Run: just bazel-lock-check
# Commit MODULE.bazel.lock alongside Cargo.toml changes
```
