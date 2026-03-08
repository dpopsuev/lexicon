---
id: papert-paradigm
title: Papert Paradigm
description: Low floor, high ceiling, wide walls for framework and library components
labels: [design, framework]
---

# Papert Paradigm

Every framework or library component must satisfy three properties from Seymour Papert's constructionist design:

| Property | Meaning | Test question |
|----------|---------|---------------|
| **Low floor** | Works with zero configuration | Can a newcomer use this with a single constructor call and no options? |
| **High ceiling** | Supports deep, sophisticated customization | Can an expert replace every policy decision via interfaces or options? |
| **Wide walls** | Enables diverse, unanticipated use cases | Can two developers combine this component in ways the author didn't plan? |

## Checklist

Before merging a new exported type:

1. **Zero-config constructor exists.** `New()` with no required arguments returns a useful, working instance. Sensible defaults everywhere.
2. **Options for tuning.** Functional options or builder pattern. Options are additive -- omitting all gives the low-floor default.
3. **Single-method interfaces.** Prefer small interfaces (1-2 methods) over large ones. Small interfaces compose via adapters and chains.
4. **Adapter functions.** Every single-method interface has a corresponding function adapter so plain functions satisfy it without boilerplate.
5. **Composition wrappers.** Multi/Chain types let users combine multiple implementations.
6. **No domain imports.** Libraries have zero imports from consumer code. Domains adapt to library interfaces, not the reverse.
7. **Pass-through default.** When a component transforms input, the default for unknown input is identity (return the input unchanged). Never panic or error on unregistered values.

## Anti-patterns

- **Mandatory configuration.** If the user must provide a non-nil dependency before the type works, the floor is too high.
- **God interface.** If an interface has 5+ methods, implementors pay a tax. Split into single-method interfaces and compose.
- **Sealed defaults.** If the default behavior cannot be overridden without forking, the ceiling is too low.
- **Domain leakage.** If a library type references a consumer's internal packages, it has escaped library scope.
