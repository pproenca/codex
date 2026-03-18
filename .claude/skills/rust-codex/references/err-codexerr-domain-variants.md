---
title: Define Domain-Specific Error Variants
impact: CRITICAL
impactDescription: Enables precise error matching and targeted recovery logic
tags: err, thiserror, CodexErr, error-variants
---

## Define Domain-Specific Error Variants

The `CodexErr` enum uses `thiserror` to define domain-specific variants for each failure mode. Each variant carries structured data so callers can match on the error type and take targeted action rather than parsing error strings.

**Incorrect (string-based error with no structure):**

```rust
#[derive(Error, Debug)]
pub enum AppError {
    #[error("something went wrong: {0}")]
    General(String),
    #[error(transparent)]
    Other(#[from] anyhow::Error),
}
```

**Correct (domain variants with structured payloads):**

```rust
#[derive(Error, Debug)]
pub enum CodexErr {
    #[error("stream disconnected before completion: {0}")]
    Stream(String, Option<Duration>),

    #[error("Codex ran out of room in the model's context window.")]
    ContextWindowExceeded,

    #[error("server returned {}", .0.status_code)]
    UnexpectedStatus(UnexpectedResponseError),

    #[error("usage limit reached")]
    UsageLimitReached(UsageLimitReachedError),

    #[error("quota exceeded")]
    QuotaExceeded,
}
```
