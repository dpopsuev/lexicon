---
id: five-s
title: "5S Workplace Organization"
description: "Sort, Set in order, Shine, Standardize, Sustain — organize the workspace for efficiency"
labels: [manufacturing, lean, tps, organization]
---

# 5S

**Core idea:** An organized workspace reduces waste and errors. Five steps, each building on the previous.

## The Five Steps

1. **Sort (Seiri)** — remove what's not needed. Delete dead code.
2. **Set in order (Seiton)** — organize what remains. Package structure.
3. **Shine (Seiso)** — clean the workspace. Lint, format, fix warnings.
4. **Standardize (Seiketsu)** — make rules for maintaining order. golangci-lint config.
5. **Sustain (Shitsuke)** — make it a habit. Pre-commit hooks, CI.

## Software 5S

| Step | Code Equivalent |
|---|---|
| Sort | Delete dead code, unused imports |
| Set in order | Package restructure (32→24 dirs) |
| Shine | golangci-lint clean, go fmt |
| Standardize | .golangci.yml, Lex rules, templates |
| Sustain | Pre-commit hooks, CI pipeline |

**Reference:** https://en.wikipedia.org/wiki/5S_(methodology)
