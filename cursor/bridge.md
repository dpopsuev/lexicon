# Cursor Bridge

Cursor loads all `.mdc` files from `.cursor/rules/` at session start.

## Bootstrap

Install the Lex bridge rule to auto-resolve remote lexicons:

```bash
lex cursor-bridge-rule --global
```

This creates `~/.cursor/rules/lex-bridge.mdc` with `alwaysApply: true`,
which triggers `resolve_lexicon` at the start of every Cursor session.

## Adaptations

- Rules use YAML frontmatter for metadata (`description`, `alwaysApply`, `globs`)
- Skills live in `.cursor/skills/<name>/SKILL.md`
- Globs control context-aware routing (only inject rule when editing matching files)
- `alwaysApply: true` means the rule is always injected regardless of context
