---
id: data-driven-programming
title: "Data-Driven Programming"
description: "Control flow determined by data, not hardcoded logic. Tables, configs, schemas drive behavior."
labels: [paradigm, data-driven, design]
---

# Data-Driven Programming

**Core idea:** Behavior is determined by data (tables, configs, schemas), not hardcoded logic. Change behavior by changing data, not code.

## Examples

| Hardcoded | Data-Driven |
|---|---|
| `if kind == "task" { ... }` | Schema defines kind behavior |
| `switch role { case "executor": ... }` | Uniform config selects shell preset |
| `timeout := 30 * time.Second` | Config: `timeout: 30s` |

## Benefits

- Change behavior without recompiling
- Non-developers can modify behavior (YAML, JSON)
- Testing: swap config, not code
- A/B testing: different configs, same code

## Anti-Patterns

- Over-engineering: making everything configurable when it never changes
- Configuration hell: so many knobs nobody knows the defaults

**Reference:** https://en.wikipedia.org/wiki/Data-driven_programming
