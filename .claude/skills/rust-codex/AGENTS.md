# Codex-RS Rust

**Version 1.0.0**  
Codex-RS Contributors  
March 2026

> **Note:**  
> This document is mainly for agents and LLMs to follow when maintaining,  
> generating, or refactoring codebases. Humans may also find it useful,  
> but guidance here is optimized for automation and consistency by AI-assisted workflows.

---

## Abstract

Comprehensive best practices guide for the Codex-RS Rust codebase, designed for AI agents and LLMs. Contains 40+ rules across 8 categories, prioritized by impact from critical (error handling, type design) to incremental (build and CI). Each rule includes detailed explanations, real-world examples from the codex-rs codebase comparing incorrect vs. correct implementations, and specific impact metrics to guide automated code generation.

---

## Table of Contents

1. [Error Handling](references/_sections.md#1-error-handling) — **CRITICAL**
   - 1.1 [Avoid unwrap in Production Code](references/err-avoid-unwrap-production.md) — CRITICAL (Prevents runtime panics that crash the entire process)
   - 1.2 [Define Domain-Specific Error Variants](references/err-codexerr-domain-variants.md) — CRITICAL (Enables precise error matching and targeted recovery logic)
   - 1.3 [Use context on Every Fallible Operation](references/err-context-every-fallible.md) — CRITICAL (Saves 5-30min per debug session by providing operation context)
   - 1.4 [Use Descriptive Context Strings Explaining the Operation](references/err-descriptive-context-strings.md) — CRITICAL (Reduces debugging time 2-5x with clear failure location)
   - 1.5 [Use let _ = for Non-Critical Send Failures](references/err-let-underscore-noncritical.md) — CRITICAL (Prevents panic during shutdown when receivers are dropped)
   - 1.6 [Use Platform-Conditional Error Variants](references/err-platform-conditional-variants.md) — CRITICAL (Prevents cross-platform compile errors on Linux-only or macOS-only code)
2. [Type Design](references/_sections.md#2-type-design) — **CRITICAL**
   - 2.1 [Prefer BTreeMap for Deterministic Output](references/type-btreemap-determinism.md) — CRITICAL (Eliminates nondeterministic serialization and test flakiness)
   - 2.2 [Return Named Structs Over Tuples](references/type-return-struct-over-tuple.md) — CRITICAL (Prevents field-order confusion and improves refactoring safety)
   - 2.3 [Use Argument Comments Before Opaque Literals](references/type-param-comment-lint.md) — CRITICAL (Prevents argument-order bugs at callsites with positional literals)
   - 2.4 [Use Enums Over Bool Parameters](references/type-enum-over-bool.md) — CRITICAL (Self-documenting callsites eliminate entire class of argument-swap bugs)
   - 2.5 [Use Exhaustive Match Without Wildcards](references/type-exhaustive-match.md) — CRITICAL (Prevents 100% of unhandled-variant bugs when enums grow)
   - 2.6 [Use Newtype Wrappers for Domain Identifiers](references/type-newtype-wrappers.md) — CRITICAL (Prevents mixing String params that represent different domains)
3. [Module Organization](references/_sections.md#3-module-organization) — **HIGH**
   - 3.1 [Avoid Creating Single-Use Helper Methods](references/mod-no-single-use-helpers.md) — HIGH (Reduces indirection and keeps logic discoverable at the call site)
   - 3.2 [Keep Modules Under 500 Lines of Code](references/mod-500-loc-limit.md) — HIGH (Reduces merge conflicts and improves code navigation in high-touch files)
   - 3.3 [Move Tests and Docs When Extracting Modules](references/mod-extract-with-docs.md) — HIGH (2-3x faster onboarding by co-locating invariants with implementation)
   - 3.4 [Prefix All Crate Names with codex-](references/mod-crate-name-prefix.md) — HIGH (Ensures consistent namespace and prevents crate name collisions)
   - 3.5 [Use Sibling Test Files Instead of Inline Test Modules](references/mod-sibling-test-files.md) — HIGH (30-50% reduction in production file size, 2x faster navigation)
4. [Serde and Wire Format](references/_sections.md#4-serde-and-wire-format) — **HIGH**
   - 4.1 [Align serde and ts-rs Annotations](references/serde-ts-export.md) — HIGH (Prevents Rust/TypeScript wire format mismatches that cause runtime errors)
   - 4.2 [Avoid skip_serializing_if for v2 API Fields](references/serde-no-skip-v2.md) — HIGH (Prevents silent field omission that breaks TypeScript type expectations)
   - 4.3 [Use Explicit Tag for Discriminated Unions](references/serde-tagged-unions.md) — HIGH (Prevents 100% of discriminated union deserialization failures)
   - 4.4 [Use Params Response Notification Naming Convention](references/serde-params-response-naming.md) — HIGH (Consistent naming eliminates guesswork when adding new API methods)
   - 4.5 [Use Plain String IDs at API Boundaries](references/serde-string-ids.md) — HIGH (Simplifies client integration and avoids UUID parsing overhead)
   - 4.6 [Use rename_all camelCase on Wire Types](references/serde-rename-camelcase.md) — HIGH (Prevents field-name mismatches between Rust and TypeScript clients)
5. [Async and Concurrency](references/_sections.md#5-async-and-concurrency) — **MEDIUM-HIGH**
   - 5.1 [Use Appropriate Channel Types for Communication](references/async-channel-selection.md) — MEDIUM-HIGH (Prevents deadlocks and resource leaks from wrong channel choice)
   - 5.2 [Use Arc Notify for Task Completion Signaling](references/async-notify-coordination.md) — MEDIUM-HIGH (Eliminates busy-waiting and reduces latency for task coordination)
   - 5.3 [Use Box::pin for Large Async Stack Frames](references/async-box-pin-stack.md) — MEDIUM-HIGH (Prevents stack overflows from deeply nested async call chains)
   - 5.4 [Use CancellationToken for Shutdown Coordination](references/async-cancellation-token.md) — MEDIUM-HIGH (Enables cooperative shutdown without leaked tasks or orphan processes)
   - 5.5 [Use let _ = tx.send() for Fire-and-Forget Channels](references/async-fire-forget-send.md) — MEDIUM-HIGH (Prevents task panics when receivers drop during normal operation)
6. [Config System](references/_sections.md#6-config-system) — **MEDIUM**
   - 6.1 [Follow the Feature Flag Lifecycle](references/cfg-feature-flag-lifecycle.md) — MEDIUM (Ensures consistent feature rollout from experimental to stable to removal)
   - 6.2 [Run just bazel-lock-update After Dependency Changes](references/cfg-bazel-lock-update.md) — MEDIUM (Prevents Bazel CI failures from stale lockfile drift)
   - 6.3 [Run just write-config-schema After Config Changes](references/cfg-write-config-schema.md) — MEDIUM (Prevents CI failures from stale JSON schema files)
   - 6.4 [Update BUILD.bazel for Compile-Time File Access](references/cfg-include-str-bazel.md) — MEDIUM (Prevents Bazel build failures when Cargo passes but Bazel does not)
7. [TUI Conventions](references/_sections.md#7-tui-conventions) — **MEDIUM**
   - 7.1 [Avoid Hardcoded White Color](references/tui-no-hardcoded-white.md) — MEDIUM (Prevents invisible text on 40%+ of terminal configurations)
   - 7.2 [Mirror Changes Between tui and tui_app_server](references/tui-mirror-implementations.md) — MEDIUM (Prevents feature drift between parallel TUI implementations)
   - 7.3 [Use Ratatui Stylize Trait Helpers](references/tui-stylize-helpers.md) — MEDIUM (Reduces styling boilerplate by 3-5x and improves readability)
   - 7.4 [Use textwrap and word_wrap_lines for Text Wrapping](references/tui-textwrap-helpers.md) — MEDIUM (3-5x less wrapping code, prevents off-by-one line breaks)
8. [Build and CI](references/_sections.md#8-build-and-ci) — **LOW-MEDIUM**
   - 8.1 [Collapse Nested if Statements](references/ci-collapsible-if.md) — LOW-MEDIUM (Passes clippy::collapsible_if lint and reduces nesting depth)
   - 8.2 [Inline Format Arguments in format Macros](references/ci-inline-format-args.md) — LOW-MEDIUM (2-3x more readable format strings, prevents CI lint failure)
   - 8.3 [Run just fmt After Rust Changes](references/ci-just-fmt.md) — LOW-MEDIUM (Prevents CI formatting check failures on every PR)
   - 8.4 [Use Scoped just fix for Clippy Linting](references/ci-just-fix-scoped.md) — LOW-MEDIUM (Catches lint issues locally, avoids slow workspace-wide Clippy builds)

---

## References

1. [https://github.com/openai/codex](https://github.com/openai/codex)
2. [https://doc.rust-lang.org/book/](https://doc.rust-lang.org/book/)
3. [https://rust-lang.github.io/rust-clippy/master/index.html](https://rust-lang.github.io/rust-clippy/master/index.html)

---

## Source Files

This document was compiled from individual reference files. For detailed editing or extension:

| File | Description |
|------|-------------|
| [references/_sections.md](references/_sections.md) | Category definitions and impact ordering |
| [assets/templates/_template.md](assets/templates/_template.md) | Template for creating new rules |
| [SKILL.md](SKILL.md) | Quick reference entry point |
| [metadata.json](metadata.json) | Version and reference URLs |