---
id: scribe-workflow
title: "Scribe Workflow"
description: "How to use Scribe MCP for artifact management: create, query, graph, admin"
labels: [planning, tools, mcp, process]
includes_labels: [pragmatic]
---

# Scribe Workflow

Scribe is a work graph for AI agents with native DAG support. Three MCP tools: `artifact`, `graph`, `admin`.

## Session Start

1. `admin action=motd` — session context (active goals, warnings, domain docs)
2. `artifact action=list kind=task status=active` — what's in flight
3. `graph action=topo_sort id=<goal>` — execution order

## Tool: artifact

CRUD + sections on work artifacts (task, spec, goal, bug, campaign, need, doc, ref, template, decision, config).

| Action | What | Key params |
|--------|------|------------|
| `create` | New artifact | kind, title, scope, parent, priority, sections, depends_on |
| `batch_create` | Multiple at once | artifacts (array) |
| `get` | Fetch by ID | id (or ids for bulk), include_edges, section_filter |
| `list` | Query/filter | kind, scope, status, parent, labels, query (FTS), sort, group_by, limit |
| `set` | Update one field | id (or ids), field, value |
| `update` | Multi-field update | id, patch (map) |
| `archive` | Mark read-only | id (or ids), cascade |
| `attach_section` | Add text block | id, name, text |
| `get_section` | Read text block | id, name |
| `detach_section` | Remove text block | id, name |

## Tool: graph

Navigate and modify artifact relationships.

| Action | What | Key params |
|--------|------|------------|
| `tree` | Parent-child hierarchy | id, depth, relation, direction |
| `briefing` | Recursive ALL edges | id |
| `topo_sort` | Dependency order | id |
| `link` | Add edge | id, relation, targets |
| `unlink` | Remove edge | id, relation, targets |
| `bulk_link` | Batch edges | edges (array of {from, relation, to}) |
| `move` | Re-parent | id, target |
| `replace` | Swap edge target | id, relation, old_target, target |

**Relations:** parent_of, depends_on, follows, justifies, implements, documents, satisfies

## Tool: admin

| Action | What |
|--------|------|
| `motd` | Session context — active goals, warnings |
| `dashboard` | Storage, staleness, health |
| `vacuum` | Delete old archived artifacts |
| `lint` | Validate schema consistency |
| `check` | Conformance check (orphans, overlaps) |
| `snapshot` | Create/list/diff/restore snapshots |

## Workflow

```
1. motd → understand scope and active work
2. list → find relevant artifacts
3. get → read spec acceptance criteria
4. create → file tasks with sections (context, checklist, acceptance)
5. link → wire depends_on and implements edges
6. set status → track progress (draft → active → complete)
7. tree/topo_sort → verify DAG structure
```
