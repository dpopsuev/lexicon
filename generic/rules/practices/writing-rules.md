---
id: writing-rules
title: Writing Effective Lexicon Entries
description: How to write rules that agents follow reliably. Based on Anthropic and OpenAI prompting research on cold-context instruction-following.
labels: [meta, lexicon, agentic, process]
always_apply: false
---

# Writing Effective Lexicon Entries

Rules loaded as cold context compete for the model's attention. These principles determine whether a rule gets followed or ignored.

## The seven principles

### 1. Lead with the rule

State the rule in the first line as a bold imperative. Do not open with history, motivation, or philosophy. The model weights the beginning of a document most heavily.

<example>
Wrong: "Code quality has been a concern since the early days of software engineering..."
Right: "**Write zero comments by default.** Add one only when the WHY is non-obvious."
</example>

### 2. One rule per file, stated as a positive imperative

"Do X" outperforms "Do not do Y" every time. Negative framing is weaker signal and requires the model to invert the instruction. Reframe prohibitions as the positive alternative.

<example>
Wrong: "Do NOT create an interface unless 2+ implementations exist."
Right: "Create an interface when two or more concrete implementations exist in this change."
</example>

### 3. Add a one-line rationale

Rules with a "because" clause generalize better to edge cases the rule does not anticipate. Without rationale, the model applies the rule too literally at its edges.

<example>
Wrong: "Never use ellipses."
Right: "Never use ellipses — TTS engines cannot pronounce them and render them as silence."
</example>

### 4. Make every rule verifiable by inspection

An agent must be able to check compliance without judgment calls. Abstract principles ("write clean code") produce inconsistent behavior across sessions. Specific, observable criteria do not.

<example>
Wrong: "Keep functions short."
Right: "Extract a function when its body exceeds 20 lines or contains more than two levels of nesting."
</example>

### 5. Add 3–5 concrete examples, tagged

Examples are the highest-signal steering mechanism. A single well-chosen example often outperforms a paragraph of instruction. Tag them so the model distinguishes examples from rules.

```
<example>
Wrong: [the anti-pattern]
Right: [the correct form]
</example>
```

### 6. Keep files under 200 lines

Files over 200 lines consume context and reduce adherence. Rules buried in the middle of a long document are underweighted (the "lost in the middle" effect). If a rule requires more than 200 lines, split it into focused sub-rules.

### 7. Remove system-specific references

A rule that mentions internal project names, tool names, or component names from one codebase is not a generic rule — it is documentation. Generic rules belong in the lexicon. Project-specific context belongs in the project's AGENTS.md.

## Audit checklist for existing entries

- [ ] Does the first line state the rule as an imperative?
- [ ] Are prohibitions reframed as positive alternatives?
- [ ] Does each rule have a one-line rationale?
- [ ] Can compliance be verified by inspection, without judgment?
- [ ] Are there 3–5 tagged examples?
- [ ] Is the file under 200 lines?
- [ ] Does the file mention internal project/tool names that don't belong here?

## References

- Anthropic prompting best practices — https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview
- Liu et al. 2023 "Lost in the Middle" — https://arxiv.org/abs/2307.03172
- OpenAI prompt engineering guide — https://platform.openai.com/docs/guides/prompt-engineering
