---
title: Avoid Hardcoded White Color
impact: MEDIUM
impactDescription: Ensures correct appearance on both light and dark terminal themes
tags: tui, color, accessibility, themes
---

## Avoid Hardcoded White Color

Do not use `.white()` in TUI code. Hardcoded white is invisible on light terminal backgrounds. Instead, rely on the default foreground color (no color set), which adapts to the user's terminal theme.

**Incorrect (invisible on light backgrounds):**

```rust
let title = Line::from(vec![
    "Session: ".white(),
    session_name.white().bold(),
]);
```

**Correct (uses default foreground, adapts to theme):**

```rust
let title = Line::from(vec![
    "Session: ".into(),
    Span::from(session_name).bold(),
]);
```
