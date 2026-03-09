---
id: human-readable-output
title: Human-Readable Output
description: Codes for machines, words for humans
labels: [conventions, output]
---

# Human-Readable Output

**Codes for machines, words for humans.**

## Machine context (use codes)

Short opaque identifiers in: JSON/wire payloads, map keys, struct tags, database columns, equality comparisons, test fixture expected values.

## Human context (use names)

Human-readable names in: CLI output, markdown reports, log messages, documentation, error messages, test failure messages.

## Dual-audience format

When both machine and human read the same line, use "Name (code)" format (e.g., "Product Bug (pb001)").

## Enforcement

- Maintain a display registry mapping codes to names.
- All human-facing formatters must use the display layer.
- Raw codes in human output are treated as bugs.
