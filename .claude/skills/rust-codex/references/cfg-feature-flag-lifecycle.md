---
title: Follow the Feature Flag Lifecycle
impact: MEDIUM
impactDescription: Ensures consistent feature rollout from experimental to stable to removal
tags: cfg, feature-flags, lifecycle, Feature-enum
---

## Follow the Feature Flag Lifecycle

Every feature flag follows the lifecycle: UnderDevelopment, Experimental, Stable, Deprecated, Removed. New features start as `UnderDevelopment` with no user-facing toggle. When ready for testing, promote to `Experimental` with a name and menu description. The `Removed` stage keeps backward compatibility with old configs.

**Incorrect (feature jumps to Stable with no lifecycle):**

```rust
pub enum Feature {
    MyNewFeature,
}

impl Feature {
    pub fn stage(&self) -> Stage {
        match self {
            Feature::MyNewFeature => Stage::Stable,
        }
    }
}
```

**Correct (follows the full lifecycle):**

```rust
pub enum Feature {
    MyNewFeature,
}

impl Feature {
    pub fn stage(&self) -> Stage {
        match self {
            Feature::MyNewFeature => Stage::Experimental {
                name: "my-new-feature",
                menu_description: "Enable the new feature for testing",
                announcement: "Try the new feature!",
            },
        }
    }
}
```
