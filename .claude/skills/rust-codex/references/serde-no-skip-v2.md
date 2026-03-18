---
title: Avoid skip_serializing_if for v2 API Fields
impact: HIGH
impactDescription: Prevents silent field omission that breaks TypeScript type expectations
tags: serde, skip-serializing, v2, api-consistency
---

## Avoid skip_serializing_if for v2 API Fields

Never use `#[serde(skip_serializing_if = "Option::is_none")]` for v2 API payload fields. The TypeScript client expects all fields to be present (as `null` when absent). Skipping fields silently changes the wire shape and breaks client-side type checking.

**Incorrect (omits field entirely when None):**

```rust
#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ThreadReadResponse {
    pub thread_id: String,
    #[serde(skip_serializing_if = "Option::is_none")]
    pub last_message: Option<String>,
}
// Wire when None: {"threadId": "..."} — missing field!
```

**Correct (field always present as null):**

```rust
#[derive(Serialize, Deserialize)]
#[serde(rename_all = "camelCase")]
pub struct ThreadReadResponse {
    pub thread_id: String,
    pub last_message: Option<String>,
}
// Wire when None: {"threadId": "...", "lastMessage": null}
```

**When NOT to use this pattern:**

- Client-to-server requests with no params may use `#[serde(skip_serializing_if = "Option::is_none")] params: Option<()>`.
