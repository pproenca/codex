---
title: Keep Modules Under 500 Lines of Code
impact: HIGH
impactDescription: Reduces merge conflicts and improves code navigation in high-touch files
tags: mod, file-size, organization, maintainability
---

## Keep Modules Under 500 Lines of Code

Target Rust modules under 500 LoC excluding tests. If a file exceeds roughly 800 LoC, add new functionality in a new module instead of extending the existing file. This applies especially to high-touch files like `app.rs`, `chat_composer.rs`, `footer.rs`, and `chatwidget.rs`.

**Incorrect (growing a large module further):**

```rust
// codex-rs/tui/src/app.rs — already 900+ LoC
// Adding 200 more lines of notification handling here

impl App {
    fn handle_notification(&mut self, notif: Notification) {
        // ... 200 lines of new notification logic ...
    }
}
```

**Correct (extract to a new sibling module):**

```rust
// codex-rs/tui/src/app.rs — keeps existing code
mod notification_handler;
use notification_handler::handle_notification;

impl App {
    fn handle_notification(&mut self, notif: Notification) {
        notification_handler::handle(self, notif);
    }
}

// codex-rs/tui/src/notification_handler.rs — new module
pub fn handle(app: &mut App, notif: Notification) {
    // ... notification logic lives here ...
}
```
