---
title: Use Params Response Notification Naming Convention
impact: HIGH
impactDescription: Consistent naming eliminates guesswork when adding new API methods
tags: serde, naming, protocol, api-design
---

## Use Params Response Notification Naming Convention

Follow the payload naming convention consistently: `*Params` for request payloads, `*Response` for responses, and `*Notification` for server-initiated notifications. Expose RPC methods as `<resource>/<method>` with singular resource names.

**Incorrect (inconsistent naming):**

```rust
pub struct ThreadReadRequest {   // Should be Params
    pub thread_id: String,
}
pub struct ThreadReadResult {    // Should be Response
    pub data: ThreadData,
}
pub struct ThreadUpdateEvent {   // Should be Notification
    pub thread_id: String,
}
```

**Correct (Params/Response/Notification convention):**

```rust
pub struct ThreadReadParams {
    pub thread_id: String,
}
pub struct ThreadReadResponse {
    pub data: ThreadData,
}
pub struct ThreadUpdateNotification {
    pub thread_id: String,
}
// RPC method: "thread/read" (singular resource)
```
