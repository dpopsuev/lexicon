---
id: abstraction-boundaries
title: Abstraction Boundaries
description: Separate generic framework models from scenario-specific implementations
labels: [architecture, design]
---

# Abstraction Boundaries

Before encoding any logic, documentation, or data structure, determine whether it is **generic** or **scenario-specific**.

## Generic

Applies to any domain, any operator, any data source. Uses only abstract concepts (e.g., your framework's core types). No hardcoded layouts, file names, or domain-specific paths.

- Document in the **core model** or generic sections.
- In code: prefer interfaces and abstract data shapes. Bind specifics via configuration or adapters.

## Scenario-specific

Tied to a current consumer or use case. Layouts, artifact directories, file names, and output formats may change per consumer.

- Document as clearly labeled **use-case sections** (e.g., "Reference implementation: X").
- Do not embed scenario details in the core model.

## Rules

1. **Core models** -- keep hierarchy and artifact concepts generic. Concrete layouts and metadata labels go in per-consumer sections.
2. **Examples and defaults** -- state whether they are generic or scenario-specific. If scenario-specific, name the scenario and note that it may change.
3. **Code** -- prefer generic interfaces. Bind domain-specific naming and layout via configuration or adapter pattern, not as the only supported shape.
