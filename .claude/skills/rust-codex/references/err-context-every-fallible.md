---
title: Use context on Every Fallible Operation
impact: CRITICAL
impactDescription: Saves 5-30min per debug session by providing operation context
tags: err, context, anyhow, error-handling
---

## Use context on Every Fallible Operation

Every `?` on a fallible operation must have a `.context()` annotation explaining what was being attempted. Without context, errors propagate as bare IO or parse errors with no indication of which step failed.

**Incorrect (opaque error with no context):**

```rust
fn load_network_proxy(path: &Path) -> anyhow::Result<ProxyConfig> {
    let codex_home = find_codex_home()?;
    let raw = std::fs::read_to_string(codex_home.join("config.toml"))?;
    let config: ConfigToml = toml::from_str(&raw)?;
    Ok(config.network_proxy)
}
```

**Correct (each fallible call explains the operation):**

```rust
fn load_network_proxy(path: &Path) -> anyhow::Result<ProxyConfig> {
    let codex_home = find_codex_home()
        .context("failed to resolve CODEX_HOME")?;
    let raw = std::fs::read_to_string(codex_home.join("config.toml"))
        .context("failed to read config file")?;
    let config: ConfigToml = toml::from_str(&raw)
        .context("failed to deserialize network tables from config")?;
    Ok(config.network_proxy)
}
```
