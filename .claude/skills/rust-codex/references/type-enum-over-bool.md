---
title: Use Enums Over Bool Parameters
impact: CRITICAL
impactDescription: Self-documenting callsites eliminate entire class of argument-swap bugs
tags: type, enums, api-design, readability
---

## Use Enums Over Bool Parameters

Avoid bool or ambiguous `Option` parameters that force callers to write unreadable code like `foo(false)` or `bar(None)`. Prefer enums, named methods, newtypes, or other idiomatic Rust shapes that make callsites self-documenting.

**Incorrect (caller intent is opaque):**

```rust
fn start_session(config: &Config, verbose: bool, sandboxed: bool) {
    // ...
}

// Callsite: which bool is which?
start_session(&config, true, false);
```

**Correct (callsite reads like documentation):**

```rust
enum Verbosity { Quiet, Verbose }
enum SandboxMode { Disabled, Enabled }

fn start_session(config: &Config, verbosity: Verbosity, sandbox: SandboxMode) {
    // ...
}

// Callsite is unambiguous
start_session(&config, Verbosity::Verbose, SandboxMode::Disabled);
```
