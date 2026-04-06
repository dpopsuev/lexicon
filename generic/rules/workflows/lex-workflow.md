---
id: lex-workflow
title: "Lex Rule Resolution Workflow"
description: "How to use Lex MCP for rule resolution, label-based filtering, and lexicon management"
labels: [planning, process, agentic]
includes_labels: [pragmatic]
---

# Lex Rule Resolution Workflow

Lex is a rule resolution engine. Two MCP tools: `lexicon`, `config`.

## Tool: lexicon

| Action | What | Key params |
|--------|------|------------|
| `resolve` | Get context-aware rules | language, files, keywords, labels, budget |
| `search` | Find rules by substring | query |
| `inspect` | List all rules/skills | type (rules/skills/all) |
| `add` | Register remote lexicon | url, ref, priority |
| `remove` | Unregister source | url |
| `enable` / `disable` | Toggle source | url |
| `sync` | Re-fetch all remotes | — |
| `list` | Show registered sources | — |

## Tool: config

| Action | What | Key params |
|--------|------|------------|
| `get` | Current config | — |
| `set` | Update setting | key, value |

## Label System

Labels are the primary query mechanism:

```
resolve labels=["pragmatic"]     → 13 core engineering rules
resolve labels=["planning"]      → pragmatic + Scribe/Lex/Locus workflows
resolve labels=["testing"]       → ROGYB, coverage matrix, BDD, TUI testing
```

**Nested labels:** Rules can declare `includes_labels` in frontmatter. Resolving `planning` expands to include all `pragmatic` rules automatically.

**`always_apply`:** Rules with this flag appear in every resolve call.

## Context-Aware Resolution

Pass context for relevance scoring:

```
resolve language=go keywords=["testing","refactoring"] files=["auth/middleware.go"]
```

The scorer ranks rules by:
1. File glob match (+10 per match)
2. Language match (+5)
3. Keyword match (+3 per match)
4. Always-apply (+1)
5. Priority weight (0-100 scale)

## Workflow

```
1. resolve labels=["planning"] → get pragmatic rules + tool workflows
2. resolve language=go keywords=[...] → get context-specific rules
3. search query="composition" → find specific rules by keyword
4. sync → refresh after lexicon repo changes
```
