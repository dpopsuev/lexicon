---
id: locus-workflow
title: Locus Architecture Workflow
description: How AI agents use Locus MCP for spatial context, architecture scanning, and design decisions
labels: [architecture, agentic]
---

# Locus Architecture Workflow

Locus is a spatial context bus available as an MCP sidecar. Use it to understand codebase structure before making design decisions.

## Before Making Changes

- For high-level design decisions, call `scan_project` to get the current architecture as a codograph (mermaid, JSON, or markdown). This surfaces coupling hotspots, god components, and dependency structure without reading every file.

## Architecture Tools

- `scan_project` -- full codograph of a repository (packages, imports, symbols, LOC, churn). Results are cached by git HEAD SHA.
- `get_hot_spots` -- identify high-risk components (high fan-in + high churn).
- `get_coupling_table` -- fan-in, fan-out, and metrics per component.
- `get_dependencies` -- fan-in and fan-out edges for a specific component.
- `diff_branches` -- structural diff between two git branches (cache-aware: repeated scans are instant).
- `diff_codographs` -- compare the latest two scans for a repo.
- `get_codograph_history` -- past scans and inter-session diffs.
- `architecture_evolution` -- scan architecture at multiple commits to visualize structural growth over time.
- `render_diagram` -- generate mermaid diagrams (dependency, C4, coupling, churn, layers, tree, classes, sequence, ER, dataflow, callgraph, state).
- `triage` -- map a natural language intent to ranked Locus tools.
