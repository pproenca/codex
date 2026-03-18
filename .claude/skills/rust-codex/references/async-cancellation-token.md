---
title: Use CancellationToken for Shutdown Coordination
impact: MEDIUM-HIGH
impactDescription: Enables cooperative shutdown without leaked tasks or orphan processes
tags: async, cancellation, shutdown, tokio-util
---

## Use CancellationToken for Shutdown Coordination

Use `tokio_util::sync::CancellationToken` to coordinate graceful shutdown across multiple async tasks. Pass a child token to each spawned task and call `cancel()` on the parent to signal all children. This is cleaner than ad-hoc boolean flags or channel-based shutdown signals.

**Incorrect (boolean flag requires polling):**

```rust
let shutdown = Arc::new(AtomicBool::new(false));
let shutdown_clone = shutdown.clone();

tokio::spawn(async move {
    loop {
        if shutdown_clone.load(Ordering::Relaxed) {
            break;
        }
        do_work().await;
        tokio::time::sleep(Duration::from_millis(100)).await;
    }
});

shutdown.store(true, Ordering::Relaxed);
```

**Correct (CancellationToken integrates with select!):**

```rust
use tokio_util::sync::CancellationToken;

let cancel = CancellationToken::new();
let child_token = cancel.child_token();

tokio::spawn(async move {
    loop {
        tokio::select! {
            _ = child_token.cancelled() => break,
            _ = do_work() => {}
        }
    }
});

// Signal all tasks to stop
cancel.cancel();
```
