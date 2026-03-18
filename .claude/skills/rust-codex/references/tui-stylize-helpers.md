---
title: Use Ratatui Stylize Trait Helpers
impact: MEDIUM
impactDescription: Reduces styling boilerplate by 3-5x and improves readability
tags: tui, ratatui, Stylize, spans
---

## Use Ratatui Stylize Trait Helpers

Use concise styling helpers from ratatui's `Stylize` trait instead of constructing `Style` objects manually. Basic spans use `"text".into()`, styled spans use `"text".red()`, `"text".dim()`, `"text".bold()`. Chain helpers for combined styles: `url.cyan().underlined()`.

**Incorrect (verbose manual Style construction):**

```rust
let line = Line::from(vec![
    Span::styled("  M", Style::default().fg(Color::Red)),
    Span::styled(" ", Style::default().add_modifier(Modifier::DIM)),
    Span::styled("tui/src/app.rs", Style::default().add_modifier(Modifier::DIM)),
]);
```

**Correct (concise Stylize helpers):**

```rust
let line = Line::from(vec![
    "  M".red(),
    " ".dim(),
    "tui/src/app.rs".dim(),
]);
```
