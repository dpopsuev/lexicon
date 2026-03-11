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

## Security Assessment

OWASP spot-check for trust boundaries touched by this spec.
If none, write: "No trust boundaries affected."

| OWASP | Finding | Mitigation |
|-------|---------|------------|

---

## Future / Deferred

Explicit non-goals and deferred work. What we chose NOT to do, and why.

> {What's out of scope? What's the path forward if we revisit?}
