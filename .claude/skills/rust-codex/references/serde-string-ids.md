---
title: Use Plain String IDs at API Boundaries
impact: HIGH
impactDescription: Simplifies client integration and avoids UUID parsing overhead
tags: serde, identifiers, api-boundary, simplicity
---

## Use Plain String IDs at API Boundaries

Prefer plain `String` IDs at the API boundary. Do UUID parsing or conversion internally if needed. This avoids forcing every client to handle UUID parsing and keeps the wire format flexible for future ID format changes.

**Incorrect (UUID type exposed at API boundary):**

```rust
use uuid::Uuid;

#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ThreadReadParams {
    pub thread_id: Uuid,
}
// Client must send: {"threadId": "550e8400-e29b-41d4-a716-446655440000"}
// Rejects non-UUID strings at deserialization
```

**Correct (String at boundary, parse internally):**

```rust
#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ThreadReadParams {
    pub thread_id: String,
}

impl ThreadReadParams {
    fn parsed_thread_id(&self) -> Result<Uuid> {
        Uuid::parse_str(&self.thread_id)
            .context("invalid thread_id format")
    }
}
```
