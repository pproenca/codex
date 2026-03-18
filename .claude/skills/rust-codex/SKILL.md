---
name: rust-codex
description: Codex-RS Rust codebase conventions covering error handling, type design, module organization, serde wire formats, async patterns, config system, TUI, and CI
---

# Codex-RS Contributors Codex-RS Rust Best Practices

A comprehensive guide encoding the conventions of the codex-rs Rust codebase across 40 rules in 8 categories, ordered by impact. These rules are derived from the project's AGENTS.md, contributor patterns (Michael Bolin, jif-oai), and CI/CD requirements.

## When to Apply

- Writing new Rust code in any `codex-rs/` crate
- Reviewing pull requests that touch Rust files under `codex-rs/`
- Adding new features, tools, or protocol types to the app-server
- Refactoring or extracting modules from large files
- Debugging error chains or serialization mismatches across the Rust/TS boundary

## Rule Categories by Priority

| # | Category | Prefix | Impact | Rules |
|---|----------|--------|--------|-------|
| 1 | Error Handling | err- | CRITICAL | 6 |
| 2 | Type Design | type- | CRITICAL | 6 |
| 3 | Module Organization | mod- | HIGH | 5 |
| 4 | Serde and Wire Format | serde- | HIGH | 6 |
| 5 | Async and Concurrency | async- | MEDIUM-HIGH | 5 |
| 6 | Config System | cfg- | MEDIUM | 4 |
| 7 | TUI Conventions | tui- | MEDIUM | 4 |
| 8 | Build and CI | ci- | LOW-MEDIUM | 4 |
| | **Total** | | | **40** |

## Quick Reference

### Error Handling (CRITICAL)
- `err-context-every-fallible` — Add .context() on every ? operator
- `err-descriptive-context-strings` — Use "failed to <verb> <noun>" pattern
- `err-let-underscore-noncritical` — Use let _ = for non-critical send failures
- `err-avoid-unwrap-production` — No unwrap() in production code
- `err-codexerr-domain-variants` — Define domain-specific error variants in CodexErr
- `err-platform-conditional-variants` — Gate platform-specific error variants with cfg

### Type Design (CRITICAL)
- `type-enum-over-bool` — Prefer enums over bool parameters
- `type-param-comment-lint` — Use /*param_name*/ before opaque literals
- `type-exhaustive-match` — List all match variants, no wildcards
- `type-btreemap-determinism` — BTreeMap for deterministic serialization
- `type-newtype-wrappers` — Newtype wrappers for domain identifiers
- `type-return-struct-over-tuple` — Named structs over tuples for returns

### Module Organization (HIGH)
- `mod-500-loc-limit` — Keep modules under 500 LoC
- `mod-sibling-test-files` — Tests in sibling _tests.rs files
- `mod-no-single-use-helpers` — No single-use helper methods
- `mod-crate-name-prefix` — Prefix all crates with codex-
- `mod-extract-with-docs` — Move tests and docs when extracting

### Serde and Wire Format (HIGH)
- `serde-rename-camelcase` — rename_all camelCase on wire types
- `serde-params-response-naming` — Params/Response/Notification naming
- `serde-no-skip-v2` — No skip_serializing_if for v2 fields
- `serde-ts-export` — Align serde and ts-rs annotations
- `serde-tagged-unions` — Explicit tag for discriminated unions
- `serde-string-ids` — Plain String IDs at API boundaries

### Async and Concurrency (MEDIUM-HIGH)
- `async-box-pin-stack` — Box::pin for large async stack frames
- `async-channel-selection` — Right channel type for each pattern
- `async-cancellation-token` — CancellationToken for shutdown
- `async-notify-coordination` — Arc<Notify> for task signaling
- `async-fire-forget-send` — let _ = tx.send() for fire-and-forget

### Config System (MEDIUM)
- `cfg-write-config-schema` — Regenerate schema after config changes
- `cfg-bazel-lock-update` — Update Bazel lockfile after dep changes
- `cfg-feature-flag-lifecycle` — Follow feature flag lifecycle stages
- `cfg-include-str-bazel` — Update BUILD.bazel for compile-time files

### TUI Conventions (MEDIUM)
- `tui-stylize-helpers` — Use Stylize trait helpers
- `tui-no-hardcoded-white` — No .white(), use default foreground
- `tui-textwrap-helpers` — Use textwrap and word_wrap_lines
- `tui-mirror-implementations` — Mirror changes in tui and tui_app_server

### Build and CI (LOW-MEDIUM)
- `ci-just-fmt` — Run just fmt after Rust changes
- `ci-just-fix-scoped` — Scoped just fix -p for Clippy
- `ci-collapsible-if` — Collapse nested if statements
- `ci-inline-format-args` — Inline format! arguments

## How to Use

1. Before writing new Rust code, review the CRITICAL rules (error handling, type design).
2. When adding protocol types, follow all serde- rules for wire format consistency.
3. When touching TUI code, apply tui- rules and check for mirror implementations.
4. Before pushing, run `just fmt` and `just fix -p <crate>` per ci- rules.
5. After config or dependency changes, run the appropriate just commands per cfg- rules.

## Reference Files

| File | Description |
|------|-------------|
| `_sections.md` | Section definitions, ordering, and impact levels |
| `err-*.md` | Error handling rules (6 files) |
| `type-*.md` | Type design rules (6 files) |
| `mod-*.md` | Module organization rules (5 files) |
| `serde-*.md` | Serde and wire format rules (6 files) |
| `async-*.md` | Async and concurrency rules (5 files) |
| `cfg-*.md` | Config system rules (4 files) |
| `tui-*.md` | TUI convention rules (4 files) |
| `ci-*.md` | Build and CI rules (4 files) |
