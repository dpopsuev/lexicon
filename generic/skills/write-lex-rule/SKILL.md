---
name: write-lex-rule
description: >
  Write a new Lex rule or skill for ~/Workspace/lexicon. Use when the user
  asks to add a rule, document a principle, or create a skill to the shared
  lexicon. Reads existing rules as reference before writing.
---

# Write Lex Rule

## Before Writing

Read at least three existing rules from `~/Workspace/lexicon/generic/rules/`
that are closest in category to the new rule. Internalize the voice and
structure before producing anything.

```bash
ls ~/Workspace/lexicon/generic/rules/
```

Pick the most relevant category directory and read its contents.

---

## Rule Anatomy

Every rule is a single `.md` file placed in the correct category directory.

### Frontmatter (required)

```yaml
---
id: kebab-case-id
title: Human-Readable Title
description: One sentence. What this rule is and when it applies. Used for search.
labels: [label1, label2]   # at least one; use existing labels where possible
---
```

Common labels: `pragmatic`, `architecture`, `design`, `process`, `agentic`,
`philosophy`, `manufacturing`, `lean`, `testing`, `security`, `conventions`.

Add `pragmatic` only for rules that are universally applicable regardless of domain.

### Body structure

```markdown
# Title

**One-sentence principle statement in bold. The invariant the reader must never forget.**

## The Problem  (optional — skip if obvious)
Why this rule exists. What goes wrong without it.

## The Principle / The Model / The Pattern
The core content. Use tables, diagrams, code blocks freely.

## Rules
Numbered, imperative, scannable. "Do X. Never Y. Always Z."

## Anti-Patterns
Concrete failure modes. Name them.

## Complements
- `other-rule-id` — one line on why they relate
```

---

## Style Invariants

1. **Principle first.** The bold H1 subline is the rule distilled to one sentence. If you can't write it, you don't understand the rule yet.
2. **Agnostic by default.** No tool names, no framework names, no language names — unless the rule is explicitly language-specific (e.g. `effective-go`).
3. **Imperative, not descriptive.** Rules say "Do X" not "X is a way to...".
4. **Tables over prose for comparisons.** If you're comparing two or more things, use a table.
5. **Complements, not duplicates.** If a rule overlaps significantly with an existing one, extend that one or add a complement reference — don't fork it.
6. **No fluff.** Every sentence must be actionable or definitional. Delete anything that doesn't change behavior.

---

## Skill Anatomy

Skills live in `~/Workspace/lexicon/generic/skills/<skill-name>/SKILL.md`.

### Frontmatter (required)

```yaml
---
name: kebab-case-name        # max 64 chars, lowercase, hyphens only
description: >               # max 1024 chars — this is the TRIGGER SPEC
  Precise description of what this skill does and when to use it.
  The agent loads the skill based on this text alone. Be specific.
---
```

### Body structure

```markdown
# Skill Title

## Trigger  (optional — clarify edge cases)

## Step N — Step Name
Instructions. Reference MCP tools by their action signatures.
Use code blocks for exact commands.

## Invariants
Numbered. Hard rules the agent must not break.
```

### Description rules

- The description is the **only text always in context**. Every session pays its token cost.
- Write it as a trigger spec: "Use when the user says X or provides Y."
- Include the domain, the trigger condition, and the output — nothing else.

---

## Placement

| What | Where |
|---|---|
| Universal principle | `rules/philosophy/` |
| Coding practice | `rules/practices/` |
| Architecture pattern | `rules/architecture/` |
| Language-specific | `rules/languages/` |
| Agentic workflow | `rules/workflows/` |
| Reusable workflow skill | `skills/<name>/SKILL.md` |

When in doubt, `philosophy/` for timeless principles, `practices/` for process.

---

## After Writing

Sync the lexicon so the new rule is discoverable:

```
lex_lexicon(action=sync)
lex_lexicon(action=search, query="<new rule title>")
```

Verify it appears in search results before declaring done.
