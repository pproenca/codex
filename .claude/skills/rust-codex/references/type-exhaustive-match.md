---
title: Use Exhaustive Match Without Wildcards
impact: CRITICAL
impactDescription: Prevents 100% of unhandled-variant bugs when enums grow
tags: type, match, exhaustive, enums
---

## Use Exhaustive Match Without Wildcards

Make `match` statements exhaustive by listing all variants explicitly. Wildcard arms (`_`) silently swallow new variants added later, leading to subtle logic bugs that the compiler would otherwise catch.

**Incorrect (wildcard hides new variants):**

```rust
match stage {
    Stage::Experimental { name, .. } => Some(name),
    _ => None,
}
```

**Correct (compiler forces handling new variants):**

```rust
match stage {
    Stage::Experimental { name, .. } => Some(name),
    Stage::UnderDevelopment
    | Stage::Stable
    | Stage::Deprecated
    | Stage::Removed => None,
}
```

**When NOT to use this pattern:**

- When matching on foreign types with hundreds of variants where exhaustiveness is impractical.
