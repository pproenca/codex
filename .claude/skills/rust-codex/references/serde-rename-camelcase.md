---
title: Use rename_all camelCase on Wire Types
impact: HIGH
impactDescription: Prevents field-name mismatches between Rust and TypeScript clients
tags: serde, camelCase, wire-format, serialization
---

## Use rename_all camelCase on Wire Types

All API wire types must use `#[serde(rename_all = "camelCase")]` so Rust snake_case fields serialize as camelCase on the wire. This is mandatory for app-server protocol types and aligns with the TypeScript client conventions.

**Incorrect (snake_case leaks to the wire):**

```rust
#[derive(Serialize, Deserialize)]
pub struct ThreadReadResponse {
    pub thread_id: String,
    pub created_at: i64,
    pub is_archived: bool,
}
// Wire: {"thread_id": "...", "created_at": 123, "is_archived": false}
```

**Correct (camelCase on the wire):**

```rust
#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ThreadReadResponse {
    pub thread_id: String,
    pub created_at: i64,
    pub is_archived: bool,
}
// Wire: {"threadId": "...", "createdAt": 123, "isArchived": false}
```

**When NOT to use this pattern:**

- Config RPC payloads use snake_case to mirror config.toml keys.
