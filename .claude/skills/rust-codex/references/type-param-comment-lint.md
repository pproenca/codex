---
title: Use Argument Comments Before Opaque Literals
impact: CRITICAL
impactDescription: Prevents argument-order bugs at callsites with positional literals
tags: type, argument-comment, lint, readability
---

## Use Argument Comments Before Opaque Literals

When a callsite passes opaque positional literals (None, booleans, numeric literals), place an exact `/*param_name*/` comment before the value. The parameter name must exactly match the callee signature. Do not add these comments for string or char literals unless the comment adds real clarity.

**Incorrect (opaque positional arguments):**

```rust
let processor = MessageProcessor::new(
    config,
    None,
    None,
    false,
    None,
);
```

**Correct (argument comments match callee signature):**

```rust
let processor = MessageProcessor::new(
    config,
    /*thread_id*/ None,
    /*policy_decider*/ None,
    /*has_in_progress_turn*/ false,
    /*blocked_request_observer*/ None,
);
```

**When NOT to use this pattern:**

- String or char literals where the value itself documents the intent.
- When you can refactor to use an enum or builder pattern instead.
