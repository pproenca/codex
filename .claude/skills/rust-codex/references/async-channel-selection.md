---
title: Use Appropriate Channel Types for Communication
impact: MEDIUM-HIGH
impactDescription: Prevents deadlocks and resource leaks from wrong channel choice
tags: async, channels, mpsc, oneshot, broadcast
---

## Use Appropriate Channel Types for Communication

Select the right tokio channel for each communication pattern: `oneshot` for single request-response, `mpsc` for producer-consumer streams, and `broadcast` for fan-out to multiple listeners. Using the wrong channel type causes either wasted memory (bounded mpsc for single send) or missed messages (unbounded for back-pressure).

**Incorrect (mpsc channel for single response):**

```rust
let (tx, mut rx) = tokio::sync::mpsc::channel(1);

// Only one value will ever be sent
tokio::spawn(async move {
    let result = compute_config().await;
    tx.send(result).await.unwrap();
});

let config = rx.recv().await.unwrap();
```

**Correct (oneshot for single response, broadcast for fan-out):**

```rust
// Single response: use oneshot
let (tx, rx) = tokio::sync::oneshot::channel();
tokio::spawn(async move {
    let result = compute_config().await;
    let _ = tx.send(result);
});
let config = rx.await.context("config task dropped")?;

// Fan-out to multiple listeners: use broadcast
let (tx, _) = tokio::sync::broadcast::channel(16);
let mut rx1 = tx.subscribe();
let mut rx2 = tx.subscribe();
```
