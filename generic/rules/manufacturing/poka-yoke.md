---
id: poka-yoke
title: "Poka-yoke (Error-Proofing)"
description: "Design systems so mistakes are impossible or immediately detected"
labels: [manufacturing, tps, lean, safety]
---

# Poka-yoke

**Core idea:** Design the system so errors are either impossible to make or immediately detected. Prevention over detection.

## Two Types

- **Prevention** — physically impossible to do wrong (USB plug only fits one way)
- **Detection** — immediately alerts when error occurs (compiler type checking)

## In Software

| Poka-yoke | Example |
|---|---|
| Type safety | `type Role string` not raw `string` |
| Compile-time check | `var _ Store = (*SQLiteStore)(nil)` |
| Linter | golangci-lint catches errors before commit |
| Build tags | `//go:build e2e` prevents accidental test execution |
| Uniform tool absence | Agent can't call tools not in its schema |

## Djinn Alignment

- Uniform = poka-yoke (tools that don't exist can't be called)
- Mirage = poka-yoke (files outside overlay can't be modified)
- depguard = poka-yoke (forbidden imports caught at lint time)

**Reference:** https://en.wikipedia.org/wiki/Poka-yoke
