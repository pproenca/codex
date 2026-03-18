---
title: Use Newtype Wrappers for Domain Identifiers
impact: CRITICAL
impactDescription: Prevents mixing String params that represent different domains
tags: type, newtype, domain-modeling, type-safety
---

## Use Newtype Wrappers for Domain Identifiers

When multiple parameters share the same primitive type but represent different domains (thread IDs, model names, API keys), wrap them in newtype structs. This makes swapped-argument bugs a compile error instead of a runtime mystery.

**Incorrect (easy to swap thread_id and model_name):**

```rust
fn create_session(
    thread_id: String,
    model_name: String,
    api_key: String,
) -> Session {
    // Caller can silently swap thread_id and model_name
    Session { thread_id, model_name, api_key }
}
```

**Correct (compiler rejects swapped arguments):**

```rust
pub struct ThreadId(pub String);
pub struct ModelName(pub String);
pub struct ApiKey(pub String);

fn create_session(
    thread_id: ThreadId,
    model_name: ModelName,
    api_key: ApiKey,
) -> Session {
    Session {
        thread_id: thread_id.0,
        model_name: model_name.0,
        api_key: api_key.0,
    }
}
```
