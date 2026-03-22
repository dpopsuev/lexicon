---
id: for-humans
title: For Humans
description: Design interfaces, APIs, and systems from human mental models outward, not from technical constraints inward
labels: [design, architecture, process]
---

# For Humans

Technology should serve human nature, not fight against it. Design from human mental models outward, not from technical constraints inward.

Based on Kenneth Reitz's "for humans" philosophy from the Python Requests library.

## Principles

### 1. Human-first interfaces

The ideal interface matches how a person *thinks* about the problem, not how the machine solves it.

```
# Technical-first (protocol leaks through)
req = urllib2.Request('http://example.com')
response = urllib2.urlopen(req)

# Human-first (intent is the interface)
response = requests.get('http://example.com')
```

The difference is not cosmetic — it is a philosophical commitment. The API surface should read like a sentence describing what the user wants, not what the system needs.

### 2. README-driven development

Write the ideal interface first. Then implement toward that vision. If the README feels awkward, the design is wrong — fix the design, not the documentation.

### 3. Simplicity that enables complexity

Hide mechanics by default. Reveal power gradually for advanced users. A beginner and an expert should use the same tool — the beginner just uses less of it.

- **Low floor**: zero-config, sensible defaults, works out of the box
- **High ceiling**: full control available when needed
- **Wide walls**: supports diverse use cases without special modes

### 4. Amplification over replacement

Technology should make humans more capable, not less relevant. Automate the tedious, preserve the meaningful. If a tool removes human judgment from a process that benefits from it, the tool is wrong.

### 5. Dignity in design

Respect what humans naturally do well. Don't force adaptation to machine constraints. Error messages should explain what happened and what to do — not dump stack traces. Status output should use names, not UUIDs. Workflows should match how people actually think about their work.

## Application

| Domain | Human-first | Not this |
|--------|------------|----------|
| CLI flags | `--priority high` | `--priority 2` |
| Error output | "issue HEG-17 not found" | "404: resource null" |
| API design | `emcee action=list` | `POST /api/v1/issues/query` with 12 required headers |
| Config | `api_key_env: LINEAR_API_KEY` | `credentials.oauth2.bearer.token_source.env_var` |
| Status | `3 issues created in 2 batches` | `{"status":200,"meta":{"count":3}}` |

## Rules

1. **Write the README first** — if the usage example feels unnatural, redesign
2. **Match mental models** — name things the way users think about them, not how they're implemented
3. **Defaults over configuration** — the common case should require zero setup
4. **Progressive disclosure** — simple things simple, complex things possible
5. **Names for humans, codes for machines** — show "Daniel" not "usr-8f3a", show "in progress" not "2"
