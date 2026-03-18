---
title: Run just write-config-schema After Config Changes
impact: MEDIUM
impactDescription: Prevents CI failures from stale JSON schema files
tags: cfg, schema, config, just-command
---

## Run just write-config-schema After Config Changes

After modifying `ConfigToml` or any nested config types in `codex-rs/core`, run `just write-config-schema` to regenerate `codex-rs/core/config.schema.json`. CI validates that the checked-in schema matches the Rust types; forgetting this step causes CI to fail.

**Incorrect (modify config type without regenerating schema):**

```rust
// codex-rs/core/src/config.rs
#[derive(Deserialize, JsonSchema)]
pub struct ConfigToml {
    pub model: Option<String>,
    pub max_tokens: Option<u32>,  // New field added
}
// Forgot to run: just write-config-schema
// CI fails: config.schema.json is stale
```

**Correct (regenerate schema after every config change):**

```rust
// codex-rs/core/src/config.rs
#[derive(Deserialize, JsonSchema)]
pub struct ConfigToml {
    pub model: Option<String>,
    pub max_tokens: Option<u32>,  // New field added
}
// Run: just write-config-schema
// Commit the updated config.schema.json alongside the Rust change
```
