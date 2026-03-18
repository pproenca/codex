---
title: Align serde and ts-rs Annotations
impact: HIGH
impactDescription: Prevents Rust/TypeScript wire format mismatches that cause runtime errors
tags: serde, ts-rs, typescript, code-generation
---

## Align serde and ts-rs Annotations

If a field or variant uses `#[serde(rename = "...")]`, add a matching `#[ts(rename = "...")]`. Always set `#[ts(export_to = "v2/")]` on v2 types so generated TypeScript lands in the correct namespace. For client-to-server request payloads, annotate optional fields with `#[ts(optional = nullable)]`.

**Incorrect (serde rename without matching ts rename):**

```rust
#[derive(Serialize, Deserialize, TS)]
#[serde(rename_all = "camelCase")]
#[ts(export)]
pub struct ModelListResponse {
    #[serde(rename = "defaultModel")]
    pub default: String,
    pub models: Vec<ModelInfo>,
}
// TypeScript type has "default" but wire has "defaultModel"
```

**Correct (serde and ts renames aligned):**

```rust
#[derive(Serialize, Deserialize, TS)]
#[serde(rename_all = "camelCase")]
#[ts(export, export_to = "v2/")]
pub struct ModelListResponse {
    #[serde(rename = "defaultModel")]
    #[ts(rename = "defaultModel")]
    pub default: String,
    pub models: Vec<ModelInfo>,
}
```
