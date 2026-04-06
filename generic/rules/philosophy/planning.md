---
id: planning
title: "Planning Label — Pragmatic Rules + Tool Workflow"
description: "Meta-label: includes all pragmatic rules plus Scribe/Lex/Locus workflow. Resolve with labels=[planning] to get the full planning context."
labels: [planning, meta]
includes_labels: [pragmatic]
always_apply: true
---

# Planning

**This is a meta-label. When you see `labels: [planning]`, apply:**

## 1. All Pragmatic Rules

Resolve `labels: [pragmatic]` — 13 rules covering:
- Testing (ROGYB), Security (STRIDE), Observability (ODD)
- Composition, SOLID, Hexagonal, For Humans
- Integrate Early, Agentic Flywheel, Off-the-shelf
- Code Smells, Design Patterns, Evolutionary Prototyping

## 2. Scribe Workflow

Before planning or coding:
- `mcp__scribe__admin motd` — get session context (active goals, warnings)
- `mcp__scribe__artifact list` — find existing specs, tasks, decisions for the scope
- `mcp__scribe__artifact get --id X` — read spec acceptance criteria, coverage matrix
- `mcp__scribe__graph briefing --id X` — understand artifact dependencies
- After coding: validate deliverables against spec acceptance criteria

## 3. Lex Workflow

Before planning or coding:
- `mcp__lex__lexicon resolve` with language, keywords, labels — get applicable rules
- `mcp__lex__lexicon search` — find rules by keyword if unsure which apply
- Apply resolved rules during implementation (ROGYB cycle, SOLID checks, etc.)

## 4. Locus Workflow

Before planning or coding:
- `mcp__locus__codograph scan_local` — scan the codebase (cached by git SHA)
- `mcp__locus__analysis preset=architecture_review` — architecture health
- `mcp__locus__analysis cycles` — verify 0 dependency cycles
- `mcp__locus__analysis drift` — check against desired state
- `mcp__locus__analysis solid_scan` — SOLID compliance
- After coding: re-scan, verify no new cycles or violations

## 5. The Planning Sequence

```
1. CONTEXT    — Scribe motd + list specs/tasks for scope
2. RULES      — Lex resolve labels=[pragmatic] + language keywords
3. SCAN       — Locus architecture_review + drift + cycles
4. PLAN       — decompose → taxonomize → compose (Planning Pyramid)
5. EXECUTE    — ROGYB per increment (Red → Orange → Green → Yellow → Blue)
6. VALIDATE   — Locus re-scan + Scribe spec acceptance criteria
7. COMMIT     — conventional commits, ship-commits skill
```

## Complements

- All 13 pragmatic rules (testing, security, observability, SOLID, hexagonal, etc.)
- `workflows/scribe-workflow` — detailed Scribe usage
- `workflows/lex-workflow` — detailed Lex usage
- `workflows/locus-workflow` — detailed Locus usage
