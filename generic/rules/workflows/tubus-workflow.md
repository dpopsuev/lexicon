---
id: tubus-workflow
title: Tubus Stable Pipes Workflow
description: How AI agents use Tubus MCP for reusable workflow definitions and agent-in-the-loop execution
labels: [tools, mcp]
---

# Tubus Stable Pipes Workflow

Tubus stores reusable workflow definitions (pipes) and walks them step-by-step with agent-in-the-loop execution. One tool, four CRUD actions, two resource kinds (pipe, run).

## Tool: `tubus`

| Action | Kind | Purpose | Key params |
|--------|------|---------|------------|
| `list` | pipe | List registered pipes | — |
| `get` | pipe | Get pipe definition | name |
| `get` | run | Get next step or report | run_id |
| `create` | pipe | Register a new pipe | name, pipe (JSON) |
| `create` | run | Start executing a pipe | name |
| `update` | run | Submit step result | run_id, step_id, output, error |

Kind auto-infers: `run_id` present → run, otherwise → pipe.

## Agent-in-the-loop cycle

```
1. tubus action=create kind=run name=convergence  → get run_id
2. tubus action=get run_id=run-1                   → next step (call, args)
3. Agent executes the step (calls the MCP tool)
4. tubus action=update run_id=run-1 step_id=X output={...}
5. Repeat 2-4 until get returns status=complete
```

## Pipe definition format

```yaml
pipes:
  convergence:
    name: convergence
    description: Three-pillar health check
    steps:
      - id: tasks
        call: scribe.artifact
        args: {action: list, scope: "$scope", kind: task}
      - id: violations
        call: locus.analysis
        args: {action: violations}
      - id: health
        call: limes.status
        args: {metric: health}
```
