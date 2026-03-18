---
title: Return Named Structs Over Tuples
impact: CRITICAL
impactDescription: Prevents field-order confusion and improves refactoring safety
tags: type, structs, return-types, readability
---

## Return Named Structs Over Tuples

When a function returns more than two values, use a named struct instead of a tuple. Tuple fields are accessed by position (`.0`, `.1`), which is fragile and unreadable. Named fields make destructuring safe and self-documenting.

**Incorrect (positional access is error-prone):**

```rust
fn parse_response(raw: &str) -> Result<(String, u64, bool)> {
    let id = extract_id(raw)?;
    let timestamp = extract_timestamp(raw)?;
    let is_final = extract_final_flag(raw)?;
    Ok((id, timestamp, is_final))
}

// Caller must remember field order
let (id, ts, done) = parse_response(&body)?;
```

**Correct (named fields are self-documenting):**

```rust
struct ParsedResponse {
    id: String,
    timestamp: u64,
    is_final: bool,
}

fn parse_response(raw: &str) -> Result<ParsedResponse> {
    Ok(ParsedResponse {
        id: extract_id(raw)?,
        timestamp: extract_timestamp(raw)?,
        is_final: extract_final_flag(raw)?,
    })
}

let resp = parse_response(&body)?;
// resp.id, resp.timestamp, resp.is_final
```
