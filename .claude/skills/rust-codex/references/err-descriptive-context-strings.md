---
title: Use Descriptive Context Strings Explaining the Operation
impact: CRITICAL
impactDescription: Reduces debugging time 2-5x with clear failure location
tags: err, context, messages, debugging
---

## Use Descriptive Context Strings Explaining the Operation

Context strings must describe what was being attempted, not what went wrong. Use the pattern "failed to <verb> <noun>" so the error chain reads as a stack of intentions.

**Incorrect (vague or redundant context):**

```rust
let token = storage.load()
    .context("error")?;
let archive = zip::ZipArchive::new(cursor)
    .context("zip problem")?;
let body = response.bytes().await
    .context("bytes")?;
```

**Correct (describes the operation being attempted):**

```rust
let token = storage.load()
    .context("failed to load auth file")?;
let archive = zip::ZipArchive::new(cursor)
    .context("Failed to open zip archive")?;
let body = response.bytes().await
    .context("Failed to read download")?;
```
