---
id: lex-workflow
title: Lex Rule Resolution Workflow
description: How AI agents use Lex MCP for prompt enrichment and rule resolution
labels: [process, agentic]
---

# Lex Rule Resolution Workflow

Lex is a prompt enrichment engine available as an MCP sidecar. It reads rules from multiple sources (.cursor/rules, CLAUDE.md, AGENTS.md, remote repos) and merges them using priority-based cascading with context-aware scoring.

## Tools

### `lexicon` — Rule resolution and management

| Action | Purpose | Key params |
|--------|---------|------------|
| `resolve` | Get context-aware rules | language, files, keywords, labels |
| `search` | Find rules by substring | query |
| `inspect` | List all rules/skills from sources | type (rules/skills/all) |
| `add` | Register a remote lexicon repo | url, ref, priority |
| `remove` | Delete a registered source | url |
| `enable` / `disable` | Toggle a source | url |
| `sync` | Re-fetch all remote repos | — |
| `list` | Show registered sources | — |

### `config` — Settings management

| Action | Purpose | Key params |
|--------|---------|------------|
| `get` | Return current config | — |
| `set` | Update a setting | key, value |

## Workflow

1. `lexicon action=resolve language=go files=["main.go"]` — get relevant rules for current context
2. `lexicon action=inspect type=rules` — see all available rules across sources
3. `lexicon action=search query="security"` — find rules by keyword
4. `lexicon action=sync` — re-fetch remote repos after changes

## Priority Model

- Local workspace rules: priority 100 (always win)
- Remote lexicons: configurable priority (default 25)
- Duplicates by name: highest priority wins
- Context scoring: file globs, language, keywords boost relevance
