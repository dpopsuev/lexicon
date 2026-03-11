---
id: scribe-workflow
title: Scribe Workflow
description: How AI agents use Scribe MCP for goal tracking, artifact lifecycle, and cross-scope awareness
labels: [process, agentic]
---

# Scribe Workflow

Scribe is an artifact store available as an MCP sidecar. Use it to stay oriented, track work, and manage artifacts.

## Session Start

1. Call `motd` or `get_current_goal` to read the north star for this workspace's scope.
2. Call `list_artifacts` with `kind=contract` to see active and draft contracts. This tells you what work is in flight and what's next.
3. If a sprint is active, call `contract_tree` on its ID to see the full board.

## Before Making Changes

- Call `get_next_contract` to find the highest-priority unblocked contract.

## During Work

- Update contract status as you progress: `set_field` to move draft -> active -> complete.
- For bulk status changes, use `batch_update_status` with a list of IDs.
- Attach architecture diagrams, design notes, or mermaid charts to contracts via `attach_section`. Retrieve them later with `get_section`.

## Creating Artifacts

- Use `create_artifact` for new specs, tasks, bugs, sprints, goals, or any artifact kind.
- The `scope` field defaults to the workspace's home scope. Override explicitly for cross-project artifacts.
- Use `parent` to build hierarchies (sprint -> contracts, contract -> sub-tasks).
- Use `depends_on` to declare sequencing between contracts.

## Cross-Scope Awareness

- `list_artifacts` defaults to the home scope (auto-detected from the workspace). To see artifacts from other projects, pass an explicit `scope`.
- `get_artifact` by ID works across all scopes regardless of the current workspace.
