---
title: Prefer BTreeMap for Deterministic Output
impact: CRITICAL
impactDescription: Eliminates nondeterministic serialization and test flakiness
tags: type, BTreeMap, determinism, serialization
---

## Prefer BTreeMap for Deterministic Output

Use `BTreeMap` instead of `HashMap` when the map contents will be serialized, compared in tests, or displayed to the user. BTreeMap iterates in sorted key order, making output deterministic and diffs stable.

**Incorrect (nondeterministic iteration order):**

```rust
use std::collections::HashMap;

fn get_git_remote_urls(cwd: &Path) -> Option<HashMap<String, String>> {
    let mut remotes = HashMap::new();
    remotes.insert("origin".into(), url.into());
    remotes.insert("upstream".into(), upstream_url.into());
    Some(remotes)
}
```

**Correct (sorted key order, deterministic output):**

```rust
use std::collections::BTreeMap;

fn get_git_remote_urls(cwd: &Path) -> Option<BTreeMap<String, String>> {
    let mut remotes = BTreeMap::new();
    remotes.insert("origin".into(), url.into());
    remotes.insert("upstream".into(), upstream_url.into());
    Some(remotes)
}
```
