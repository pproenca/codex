---
title: Collapse Nested if Statements
impact: LOW-MEDIUM
impactDescription: Passes clippy::collapsible_if lint and reduces nesting depth
tags: ci, clippy, collapsible-if, readability
---

## Collapse Nested if Statements

Always collapse nested `if` statements into a single `if` with `&&` per the `clippy::collapsible_if` lint. Unnecessary nesting wastes horizontal space and obscures the combined condition.

**Incorrect (nested if statements):**

```rust
fn should_retry(err: &CodexErr, attempt: u32) -> bool {
    if err.is_transient() {
        if attempt < MAX_RETRIES {
            return true;
        }
    }
    false
}
```

**Correct (collapsed into single condition):**

```rust
fn should_retry(err: &CodexErr, attempt: u32) -> bool {
    if err.is_transient() && attempt < MAX_RETRIES {
        return true;
    }
    false
}
```
