---
id: emcee-workflow
title: Emcee Issue Management Workflow
description: How AI agents use Emcee MCP for unified issue management across Linear, GitHub, and Jira
labels: [process, agentic]
---

# Emcee Issue Management Workflow

Emcee is a unified issue tracker available as an MCP sidecar. Two tools: `emcee` (issue CRUD) and `emcee_manage` (supporting entities).

## Tools

### `emcee` — Issue CRUD

| Action | Purpose | Key params |
|--------|---------|------------|
| `list` | List issues | backend, status, assignee, limit |
| `get` | Get issue details | ref (e.g. `linear:HEG-17`) |
| `create` | Create issue | backend, title, description, priority, status, parent_id, project_id, assignee |
| `update` | Update issue | ref, title, description, status, priority |
| `search` | Full-text search | backend, query, limit |
| `children` | List sub-issues | ref |
| `bulk_create` | Batch create | backend, issues (JSON array) |
| `bulk_update` | Batch update | backend, issues (JSON array of {ref, ...fields}) |

### `emcee_manage` — Supporting Entities

| Action | Purpose | Key params |
|--------|---------|------------|
| `doc_list` | List documents | backend, limit |
| `doc_create` | Create document | backend, title, content, project_id |
| `project_list` | List projects | backend, limit |
| `project_create` | Create project | backend, name, description |
| `initiative_list` | List initiatives | backend, limit |
| `initiative_create` | Create initiative | backend, name, description |
| `label_list` | List labels | backend |
| `label_create` | Create label | backend, name, color |

## Ref Format

`backend:key` — e.g. `linear:HEG-17`, `github:owner/repo#42`. Used for get, update, and children.

## Values

- **Status**: backlog, todo, in_progress, in_review, done, canceled
- **Priority**: urgent, high, medium, low
- **Backend**: defaults to `linear` for list ops; required for create/update

## Workflow

1. `emcee action=list` to discover issues
2. `emcee action=get ref=linear:HEG-17` for details
3. `emcee action=create backend=linear title="..." priority=high` to create
4. `emcee action=update ref=linear:HEG-17 status=done` to update
5. `emcee action=bulk_create issues='[{"title":"..."},...]'` for batch ops

## Entity Model

- **Issues** — work items with status, priority, assignee, labels
- **Documents** — rich text (markdown), no status/priority, linked to projects
- **Projects** — group issues and documents
- **Initiatives** — strategic objectives grouping projects
- **Labels** — team-scoped tags for issues
