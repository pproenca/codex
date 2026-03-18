---
title: Use Explicit Tag for Discriminated Unions
impact: HIGH
impactDescription: Ensures consistent discriminator field across Rust and TypeScript
tags: serde, tagged-union, discriminated, enums
---

## Use Explicit Tag for Discriminated Unions

For discriminated unions, use explicit tagging in both serde and ts-rs: `#[serde(tag = "type", ...)]` and `#[ts(tag = "type", ...)]`. This ensures the TypeScript client sees a proper discriminated union with a `type` field for narrowing.

**Incorrect (default serde tagging, no ts tag):**

```rust
#[derive(Serialize, Deserialize, TS)]
#[serde(rename_all = "camelCase")]
pub enum TurnEvent {
    TextDelta { delta: String },
    ToolCall { call_id: String, name: String },
    Completed,
}
// Wire: {"TextDelta": {"delta": "..."}} — external tagging
```

**Correct (explicit type tag in both serializers):**

```rust
#[derive(Serialize, Deserialize, TS)]
#[serde(tag = "type", rename_all = "camelCase")]
#[ts(tag = "type")]
pub enum TurnEvent {
    TextDelta { delta: String },
    ToolCall { call_id: String, name: String },
    Completed,
}
// Wire: {"type": "textDelta", "delta": "..."} — internal tagging
```
