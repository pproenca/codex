---
title: Use let _ = for Non-Critical Send Failures
impact: CRITICAL
impactDescription: Prevents panic during shutdown when receivers are dropped
tags: err, channels, send, shutdown-safety
---

## Use let _ = for Non-Critical Send Failures

When sending on a channel where the receiver may already be dropped (during shutdown, cleanup, or fire-and-forget scenarios), use `let _ = tx.send(...)` to discard the Result. An unwrap or `?` on a closed channel causes a panic that crashes the process during orderly shutdown.

**Incorrect (panics if receiver is dropped):**

```rust
async fn cleanup_realtime(events_tx: &Sender<Event>) {
    events_tx.send(Event::SessionClosed).await.unwrap();
    fanout_task.await.unwrap();
    sess.conversation.shutdown().await.unwrap();
}
```

**Correct (gracefully ignores closed channels):**

```rust
async fn cleanup_realtime(events_tx: &Sender<Event>) {
    let _ = events_tx.send(Event::SessionClosed).await;
    let _ = fanout_task.await;
    let _ = sess.conversation.shutdown().await;
}
```
