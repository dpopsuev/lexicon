---
id: commit-conventions
title: Commit Message Conventions
description: Conventional commit format, subject line rules, and what never to put in a commit message.
labels: [git, conventions, output]
---

# Commit Message Conventions

Write commit messages that tell a reader what changed and why, without narrating which files you touched.

## Format

```
<type>: <what changed>
```

Types: `feat` `fix` `refactor` `test` `docs` `chore` `ci`

- Lowercase. No period at the end. 72 characters maximum.
- Subject line answers: "what does this commit do?" — not "what files did I change?"

<example>
feat: add context window battery widget to status bar
fix: input box renders at max height on launch — use callable window.height
refactor: remove streaming_pairs widget, wire StreamingBuffer via event channel
test: add PTY regression for input field height at launch
</example>

## What belongs in the subject

The change itself, stated as a verb phrase. "add", "fix", "remove", "wire", "extract", "rename", "update".

## What does not belong in a commit message

- Lists of files changed — that is what `git diff --stat` is for
- Ticket or issue IDs in the subject line — put them in the body if needed
- "WIP", "progress", "various fixes" — commit when it is done
- Apologies or explanations of why the code is messy

<example>
Wrong: "TRANSMUTE-42: fix various bugs across tui_app.py, tui_base.py, widgets.py"
Right: "fix: StreamingBuffer flushes on newline only, not eagerly per chunk"
</example>

## Body (optional)

Add a blank line after the subject, then a short paragraph if the why is non-obvious. Reference a ticket ID here if relevant. Keep the body under 10 lines.

## Complements

- `code-is-a-liability` — each commit should be the smallest correct change
- `senior-engineer-mindset` — one semantic change per commit
