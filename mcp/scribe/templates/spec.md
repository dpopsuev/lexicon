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

## Blast Radius

For each change area, state the worst-case impact if it fails. Size defenses proportionally.

| Change Area | Blast Radius | Worst Case | Defense |
|-------------|-------------|------------|---------|
| {component} | small / medium / large | {what breaks if this fails} | {mitigation: test gate, canary, rollback, approval} |

If all changes are internal with no deployment impact: "Internal logic only -- no blast radius beyond test suite."

---

## Security Assessment

**Step 1: Identify trust boundaries** touched by this spec. A trust boundary exists wherever data or control crosses between components with different privilege levels.

**Step 2: STRIDE each boundary:**

| Boundary | S (Spoofing) | T (Tampering) | R (Repudiation) | I (Info Disclosure) | D (DoS) | E (Elevation) |
|----------|---|---|---|---|---|---|
| {boundary description} | {finding or N/A} | | | | | |

**Step 3: OWASP spot-check** for applicable categories:

| OWASP | Finding | Mitigation |
|-------|---------|------------|

If no trust boundaries: "No trust boundaries affected."
See `trust-boundaries` and `defense-in-depth` Lex rules.

---

## Future / Deferred

Explicit non-goals and deferred work. What we chose NOT to do, and why.

> {What's out of scope? What's the path forward if we revisit?}
