---
id: spec-template
title: Spec Template
kind: spec
description: Design and analysis artifact — the thinking document
labels: [process, template]
---

# {Spec Title}

> **Status:** `draft` | `active` | `complete`

---

## Problem

Context, pain points, impacted users, constraints. Why does this spec exist?

> {What is broken, missing, or suboptimal? Who feels the pain? What constraints apply?}

---

## Decision

Options considered, trade-offs, and the chosen approach with rationale.

> {What options exist? What are the trade-offs? Why this one?}

---

## What Changes

Concrete changes organized by component or concern. Each change has Gherkin acceptance criteria.

### 1. {Change area}

- **Given** {precondition}
- **When** {action}
- **Then** {expected outcome}

### 2. {Change area}

- **Given** {precondition}
- **When** {action}
- **Then** {expected outcome}

---

## Architecture

(Optional. Required when changing component boundaries, data flow, or interfaces.)

### Current Architecture

> Mermaid diagram or prose describing the system before this spec.

### Desired Architecture

> Mermaid diagram or prose describing the system after this spec.

---

## Dependency Direction

Map each change area to the hexagonal layer it touches. Verify dependency arrows point inward (adapter -> port -> domain). Flag any outward dependency as a violation requiring justification.

| Change Area | Layer | Depends On | Direction Valid? |
|-------------|-------|-----------|-----------------|
| {component} | domain / port / adapter / infra | {what it imports} | Yes / No (justify) |

Layers (inward = allowed): `infra -> adapter -> port -> domain`

If no structural changes: "No dependency changes."

---

## Pattern Rationale

For each design pattern introduced or modified by this spec (see `design-patterns` rule):

| Pattern | Where | Problem It Solves | Alternative Considered |
|---------|-------|-------------------|----------------------|
| {name} | {module/component} | {why this pattern?} | {what you'd do without it} |

If no patterns: "Straight-line code -- no patterns needed."

Smell-driven refactorings (see `code-smells` rule), if applicable:

| Smell Detected | Location | Refactoring Applied | Resulting Pattern |
|----------------|----------|--------------------|--------------------|
| {smell} | {module} | {technique} | {pattern or "none"} |

If no refactorings: "No smells addressed."

---

## Security Assessment

OWASP spot-check for trust boundaries touched by this spec.
If none, write: "No trust boundaries affected."

| OWASP | Finding | Mitigation |
|-------|---------|------------|

---

## Future / Deferred

Explicit non-goals and deferred work. What we chose NOT to do, and why.

> {What's out of scope? What's the path forward if we revisit?}
