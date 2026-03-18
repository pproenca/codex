---
title: Use Arc Notify for Task Completion Signaling
impact: MEDIUM-HIGH
impactDescription: Eliminates busy-waiting and reduces latency for task coordination
tags: async, Notify, signaling, coordination
---

## Use Arc Notify for Task Completion Signaling

Use `Arc<tokio::sync::Notify>` when one task must wait for another to reach a specific state. Unlike channels, Notify carries no data and avoids allocation per signal. It is ideal for "task X is done" or "output is ready" coordination.

**Incorrect (channel-based signaling wastes allocation):**

```rust
let (done_tx, done_rx) = tokio::sync::oneshot::channel::<()>();

tokio::spawn(async move {
    process_output(&mut buffer).await;
    let _ = done_tx.send(());
});

done_rx.await.ok();
drain_remaining(&buffer);
```

**Correct (Notify for zero-data signaling):**

```rust
let output_drained = Arc::new(Notify::new());
let notify_clone = output_drained.clone();

tokio::spawn(async move {
    process_output(&mut buffer).await;
    notify_clone.notify_one();
});

output_drained.notified().await;
drain_remaining(&buffer);
```
