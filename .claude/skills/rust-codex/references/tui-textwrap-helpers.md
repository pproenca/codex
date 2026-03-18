---
title: Use textwrap and word_wrap_lines for Text Wrapping
impact: MEDIUM
impactDescription: 3-5x less wrapping code, prevents off-by-one line breaks
tags: tui, textwrap, wrapping, ratatui
---

## Use textwrap and word_wrap_lines for Text Wrapping

Always use `textwrap::wrap` to wrap plain strings. For ratatui `Line` values, use the helpers in `tui/src/wrapping.rs` (`word_wrap_lines`, `word_wrap_line`). For indented wrapped lines, use `initial_indent` and `subsequent_indent` from `textwrap::Options` instead of writing custom logic.

**Incorrect (manual character-counting wrapping):**

```rust
fn wrap_text(text: &str, width: usize) -> Vec<String> {
    let mut lines = vec![];
    let mut current = String::new();
    for word in text.split_whitespace() {
        if current.len() + word.len() + 1 > width {
            lines.push(current.clone());
            current.clear();
        }
        if !current.is_empty() { current.push(' '); }
        current.push_str(word);
    }
    if !current.is_empty() { lines.push(current); }
    lines
}
```

**Correct (use textwrap for plain strings, helpers for Lines):**

```rust
use textwrap::{wrap, Options};

// Plain string wrapping
let wrapped = wrap(text, Options::new(width)
    .initial_indent("  ")
    .subsequent_indent("  "));

// Ratatui Line wrapping
use crate::wrapping::word_wrap_lines;
let wrapped_lines = word_wrap_lines(&lines, width);
```
