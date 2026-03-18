---
title: Avoid unwrap in Production Code
impact: CRITICAL
impactDescription: Prevents runtime panics that crash the entire process
tags: err, unwrap, clippy, panic-safety
---

## Avoid unwrap in Production Code

The codebase denies `clippy::unwrap_used` in production code. Every `unwrap()` is a latent panic. Use `?` with context, `unwrap_or`, `unwrap_or_else`, or `if let` instead. Reserve `unwrap()` exclusively for test code where a panic is the intended assertion mechanism.

**Incorrect (panics on None or Err in production):**

```rust
fn get_active_model(config: &Config) -> String {
    let model = config.model.as_ref().unwrap();
    let provider = config.provider.as_ref().unwrap();
    format!("{provider}/{model}")
}
```

**Correct (propagates errors with context):**

```rust
fn get_active_model(config: &Config) -> anyhow::Result<String> {
    let model = config.model.as_ref()
        .context("no model configured")?;
    let provider = config.provider.as_ref()
        .context("no provider configured")?;
    Ok(format!("{provider}/{model}"))
}
```
