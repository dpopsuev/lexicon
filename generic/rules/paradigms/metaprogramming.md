---
id: metaprogramming
title: "Metaprogramming"
description: "Programs that write or manipulate other programs. Code generation, reflection, macros."
labels: [paradigm, metaprogramming, code-generation]
---

# Metaprogramming

**Core idea:** Programs that treat other programs as data — they can read, generate, analyze, or transform code.

## Types

| Type | What | Go Example |
|---|---|---|
| **Code generation** | Generate source code at build time | `go generate`, protobuf, stringer |
| **Reflection** | Inspect types at runtime | `reflect` package |
| **Templates** | Parameterized code | `text/template`, generics |
| **Macros** | Code transformation at compile time | Not in Go |
| **DSLs** | Domain-specific languages | YAML configs, Gherkin specs |

## Go's Approach

Go is deliberately limited in metaprogramming:
- `go generate` for build-time code generation
- `reflect` for runtime inspection (use sparingly)
- Generics (Go 1.18+) for parameterized types
- No macros, no eval, no runtime code modification

## Djinn Alignment

- Parchment Schema = DSL for artifact validation
- Ordo rules = DSL for convention enforcement
- Uniform YAML = DSL for agent configuration
- Gherkin specs = DSL for acceptance criteria

**Reference:** https://en.wikipedia.org/wiki/Metaprogramming
