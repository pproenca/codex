---
title: Run just fmt After Rust Changes
impact: LOW-MEDIUM
impactDescription: Prevents CI formatting check failures on every PR
tags: ci, formatting, rustfmt, just-command
---

## Run just fmt After Rust Changes

Run `just fmt` in the `codex-rs` directory automatically after finishing Rust code changes. Do not ask for approval to run it. This runs `cargo fmt` with the project's rustfmt configuration and ensures all code passes the CI formatting check.

**Incorrect (push unformatted code, CI fails):**

```rust
fn process(config:&Config,input:   String)->Result<Output>{
    let result=config.handler.handle(  input )?;
    Ok(result)
}
// Pushed without running just fmt — CI rejects the PR
```

**Correct (run just fmt before committing):**

```rust
fn process(config: &Config, input: String) -> Result<Output> {
    let result = config.handler.handle(input)?;
    Ok(result)
}
// After editing: just fmt (in codex-rs directory)
// Then commit the formatted code
```
