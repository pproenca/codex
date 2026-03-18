---
title: Use Platform-Conditional Error Variants
impact: CRITICAL
impactDescription: Prevents cross-platform compile errors on Linux-only or macOS-only code
tags: err, cfg, platform, conditional-compilation
---

## Use Platform-Conditional Error Variants

When error variants wrap platform-specific types (e.g., Linux seccomp or landlock), gate them with `#[cfg(target_os = "...")]`. This keeps the enum compilable on all platforms without stubbing out error types.

**Incorrect (fails to compile on macOS):**

```rust
#[derive(Error, Debug)]
pub enum SandboxErr {
    #[error("sandbox denied exec")]
    Denied { output: Box<ExecToolCallOutput> },

    #[error("seccomp setup error")]
    SeccompInstall(#[from] seccompiler::Error),

    #[error("seccomp backend error")]
    SeccompBackend(#[from] seccompiler::BackendError),
}
```

**Correct (compiles on all platforms):**

```rust
#[derive(Error, Debug)]
pub enum SandboxErr {
    #[error("sandbox denied exec")]
    Denied { output: Box<ExecToolCallOutput> },

    #[cfg(target_os = "linux")]
    #[error("seccomp setup error")]
    SeccompInstall(#[from] seccompiler::Error),

    #[cfg(target_os = "linux")]
    #[error("seccomp backend error")]
    SeccompBackend(#[from] seccompiler::BackendError),
}
```

**When NOT to use this pattern:**

- When the error type is available on all platforms via a cross-platform crate.
