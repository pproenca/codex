---
title: Use Box::pin for Large Async Stack Frames
impact: MEDIUM-HIGH
impactDescription: Prevents stack overflows from deeply nested async call chains
tags: async, Box-pin, stack-management, futures
---

## Use Box::pin for Large Async Stack Frames

Async functions capture their entire stack frame in the returned Future. When async methods call other async methods recursively or in deep chains (common in ThreadManager), each level multiplies the frame size. Wrap large async returns in `Box::pin(...)` to move the future to the heap and keep the stack bounded.

**Incorrect (unbounded stack growth from chained async calls):**

```rust
impl ThreadManager {
    async fn start_thread(&self, config: ThreadConfig) -> Result<()> {
        self.start_thread_with_tools(config, vec![]).await
    }

    async fn start_thread_with_tools(
        &self, config: ThreadConfig, tools: Vec<Tool>,
    ) -> Result<()> {
        self.state.spawn_thread(config, tools).await
    }
}
```

**Correct (Box::pin bounds the stack frame):**

```rust
impl ThreadManager {
    fn start_thread(&self, config: ThreadConfig) -> BoxFuture<'_, Result<()>> {
        Box::pin(self.start_thread_with_tools(config, vec![]))
    }

    fn start_thread_with_tools(
        &self, config: ThreadConfig, tools: Vec<Tool>,
    ) -> BoxFuture<'_, Result<()>> {
        Box::pin(self.state.spawn_thread(config, tools))
    }
}
```
