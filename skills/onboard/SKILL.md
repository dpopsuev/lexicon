---
name: onboard
description: Teach newcomers the repo purpose, architecture, build/test flow, and safe change zones.
labels: [onboarding, documentation]
---

# /onboard

## Purpose
Teach newcomers the repository's purpose, architecture, build/test flow, and safe change zones.

## Behavior

1. **Scan the project** -- read README, top-level source layout, dependency manifest (go.mod, package.json, Cargo.toml, etc.), and any architecture docs.
2. **Summarize the project** -- one paragraph on what the project does and who it's for.
3. **Architecture overview** -- list major packages/modules, their responsibilities, and key abstractions.
4. **Build / test / run** -- provide the exact commands to build, test, and run the project.
5. **Safe vs risky zones** -- identify areas safe for newcomers to modify vs areas requiring deeper knowledge.
6. **Planning pointers** -- link to relevant rules, specs, or planning artifacts if they exist.

## Constraints

- Do NOT modify any code or files. This is a read-only skill.
- Keep summaries concise -- a newcomer should be productive in under 10 minutes.
- Adapt output to the actual project structure; do not assume any specific framework or language.
