---
title: Use let _ = tx.send() for Fire-and-Forget Channels
impact: MEDIUM-HIGH
impactDescription: Prevents task panics when receivers drop during normal operation
tags: async, fire-and-forget, channels, send-safety
---

## Use let _ = tx.send() for Fire-and-Forget Channels

When sending events on a channel where the receiver may have been dropped (e.g., UI event streams, analytics, logging), always use `let _ = tx.send(...)`. The send may fail during normal shutdown or when the consumer has moved on, and that failure is not an error.

**Incorrect (panics or propagates error on dropped receiver):**

```rust
async fn emit_events(events_tx: &Sender<Event>, data: EventData) {
    events_tx.send(Event::TextDelta(data.delta)).await?;
    events_tx.send(Event::ToolCall(data.call)).await?;
    events_tx.send(Event::Completed).await?;
}
```

**Correct (gracefully ignores send failures):**

```rust
async fn emit_events(events_tx: &Sender<Event>, data: EventData) {
    let _ = events_tx.send(Event::TextDelta(data.delta)).await;
    let _ = events_tx.send(Event::ToolCall(data.call)).await;
    let _ = events_tx.send(Event::Completed).await;
}
```
