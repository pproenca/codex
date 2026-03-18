---
title: Avoid Creating Single-Use Helper Methods
impact: HIGH
impactDescription: Reduces indirection and keeps logic discoverable at the call site
tags: mod, helpers, indirection, code-organization
---

## Avoid Creating Single-Use Helper Methods

Do not create small helper methods that are referenced only once. Single-use helpers add indirection without reuse benefit, making the code harder to follow. Inline the logic at the call site instead.

**Incorrect (single-use helper adds pointless indirection):**

```rust
impl ThreadManager {
    fn process_turn(&mut self, turn: Turn) -> Result<()> {
        let tools = self.collect_tools_for_turn(&turn)?;
        self.execute_turn_with_tools(turn, tools)
    }

    // Only called once from process_turn
    fn collect_tools_for_turn(&self, turn: &Turn) -> Result<Vec<Tool>> {
        self.tools.iter()
            .filter(|t| t.applies_to(&turn.model))
            .cloned()
            .collect()
    }
}
```

**Correct (logic inlined at the single call site):**

```rust
impl ThreadManager {
    fn process_turn(&mut self, turn: Turn) -> Result<()> {
        let tools: Vec<Tool> = self.tools.iter()
            .filter(|t| t.applies_to(&turn.model))
            .cloned()
            .collect();
        self.execute_turn_with_tools(turn, tools)
    }
}
```
