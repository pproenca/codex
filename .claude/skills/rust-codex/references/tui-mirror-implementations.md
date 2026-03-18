---
title: Mirror Changes Between tui and tui_app_server
impact: MEDIUM
impactDescription: Prevents feature drift between parallel TUI implementations
tags: tui, tui-app-server, mirroring, consistency
---

## Mirror Changes Between tui and tui_app_server

When a change lands in `codex-rs/tui`, check whether `codex-rs/tui_app_server` has a parallel implementation of the same behavior. If it does, reflect the change there too unless there is a documented reason not to. The two crates share UI logic and must stay in sync.

**Incorrect (change only in tui, tui_app_server diverges):**

```rust
// codex-rs/tui/src/bottom_pane/footer.rs
fn render_status_bar(&self, area: Rect, buf: &mut Buffer) {
    // New: show active model in footer
    let model_span = self.active_model.as_str().cyan();
    // ...
}

// codex-rs/tui_app_server/src/bottom_pane/footer.rs
// Still shows old footer without model — user sees inconsistency
```

**Correct (both implementations updated together):**

```rust
// codex-rs/tui/src/bottom_pane/footer.rs
fn render_status_bar(&self, area: Rect, buf: &mut Buffer) {
    let model_span = self.active_model.as_str().cyan();
    // ...
}

// codex-rs/tui_app_server/src/bottom_pane/footer.rs
fn render_status_bar(&self, area: Rect, buf: &mut Buffer) {
    let model_span = self.active_model.as_str().cyan();
    // Same change mirrored
}
```
