---
title: Inline Format Arguments in format Macros
impact: LOW-MEDIUM
impactDescription: 2-3x more readable format strings, prevents CI lint failure
tags: ci, clippy, format, inline-args
---

## Inline Format Arguments in format Macros

Always inline variables directly into `{}` in `format!`, `println!`, `write!`, and similar macros when the variable name matches the format position. This satisfies `clippy::uninlined_format_args` and makes the template more readable.

**Incorrect (separate format arguments):**

```rust
let msg = format!("failed to load config from {}", path.display());
println!("Thread {} started with model {}", thread_id, model_name);
tracing::info!("Processing request {}", request_id);
```

**Correct (inlined format arguments):**

```rust
let display = path.display();
let msg = format!("failed to load config from {display}");
println!("Thread {thread_id} started with model {model_name}");
tracing::info!("Processing request {request_id}");
```
