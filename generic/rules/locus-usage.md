---
id: locus-usage
title: Locus — Codebase Intelligence
description: Use Locus MCP tools for codebase exploration instead of bash glue
labels: [tools, mcp, architecture]
---

# Locus — Codebase Intelligence

Locus is a spatial context bus for AI agents. It scans repositories and caches architecture, dependency graphs, churn, hot spots, and symbols. **Use Locus instead of bash commands for codebase exploration.**

## When to use Locus

| Question | Locus tool | NOT this |
|----------|-----------|----------|
| What packages exist? | `codograph status` | `ls`, `find -type d` |
| What depends on what? | `analysis coupling` | `grep import` |
| Where are the hot spots? | `analysis coupling view=hot_spots` | `wc -l`, `git log --stat` |
| What's the architecture? | `analysis preset=architecture_review` | `tree`, `ls -R` |
| Find a symbol/concept | `analysis search query="SourceReader"` | `grep -rn` |
| Check layer violations | `analysis violations` | manual import tracing |
| Render a diagram | `render_diagram type=zones` | ASCII art |

## Workflow

1. **Scan first**: `codograph scan_local path=/repo intent=architecture` — returns a `cache_key`
2. **Analyze**: `analysis cache_key=<key> preset=architecture_review` — use the cache_key
3. **Render**: `render_diagram cache_key=<key> type=zones` — visual architecture

## Intent levels

- `architecture` — fast scan, package-level graph
- `coupling` — adds coupling/cohesion metrics
- `health` — default, adds churn and complexity
- `full` — everything including symbol-level analysis

## Output formats

- Default: ~50 token summary (good for quick checks)
- `format=json`: full structured data
- `format=summary`: <500 token overview
- `format=facts`: assertion-style statements

## Rules

1. **Never use bash to answer codebase structure questions** when Locus is available
2. **Never parse Locus output with python/jq** — if the output isn't usable, that's a missing feature to file
3. **Always scan before analyzing** — stale caches return stale results
4. **Use `intent` to control scan depth** — don't run `full` when `architecture` suffices
5. **Cache keys are tied to git HEAD** — rescan after commits
