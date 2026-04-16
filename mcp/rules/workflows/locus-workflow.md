---
id: locus-workflow
title: "Locus Architecture Workflow"
description: "How to use Locus MCP for architecture scanning, dependency analysis, and drift detection"
labels: [planning, tools, mcp, architecture]
includes_labels: [pragmatic]
---

# Locus Architecture Workflow

Locus is a codebase intelligence engine. Three MCP tools: `codograph`, `analysis`, `render_diagram`.

## Tool: codograph

Scan and compare repository architectures. Results cached by git HEAD SHA.

| Action | What | Key params |
|--------|------|------------|
| `scan_local` | Scan local repo | path, intent (architecture/coupling/health/full) |
| `scan_remote` | Scan GitHub repo | url, ref |
| `set_desired_state` | Persist architecture rules | path, layers |
| `get_desired_state` | Read saved rules | path |
| `status` | Cache status | path |
| `diff` | Compare two scans | branch_a, branch_b |
| `history` | Past scans | path, last |

## Tool: analysis

Core dependency and architecture analysis.

| Action | What | Key params |
|--------|------|------------|
| `deps` | Fan-in/fan-out for a component | path, component |
| `impact` | What breaks if this changes | path, component |
| `coupling` | Coupling metrics | path, view (hot_spots/edges), churn_days |
| `cycles` | Dependency cycles | path |
| `violations` | Layer violations | path |
| `drift` | Desired vs actual state | path |
| `component` | Deep dive on one component | path, component |
| `search` | Find by keyword | path, query |
| `preset` | Pre-built analysis | path, preset (architecture_review/health_check/pre_pr) |
| `solid_scan` | SOLID compliance | path |
| `callers` | Who calls this symbol | path, symbol |

## Tool: render_diagram

Generate Mermaid diagrams.

| Type | What |
|------|------|
| `dependency` | Package dependency graph |
| `c4` | C4 context diagram |
| `coupling` | Coupling heat map |
| `layers` | Layer architecture |
| `tree` | Directory tree |
| `classes` | Type/interface diagram |
| `hexa` | Hexagonal architecture validation |
| `zones` | Zone overview |

## Workflow

```
1. codograph scan_local → cache the repo (returns cache_key)
2. analysis preset=architecture_review → quick health check
3. analysis cycles → verify 0 cycles
4. analysis drift → compare against desired state
5. analysis coupling view=hot_spots → identify risky components
6. render_diagram type=dependency → visualize structure

After changes:
7. codograph scan_local → re-scan (new SHA invalidates cache)
8. analysis drift → verify no new violations
9. analysis cycles → verify still 0 cycles
```

## Key Presets

| Preset | What it does |
|--------|-------------|
| `architecture_review` | Components, edges, cycles, hot spots — one call |
| `health_check` | Coverage, complexity, churn — code health |
| `pre_pr` | Quick diff-based check before PR |

## Desired State

Set architectural rules that drift detection validates against:

```
codograph set_desired_state path=/workspace layers=["cmd", "app", "domain", "infra"]
analysis drift → reports violations against these layers
```
