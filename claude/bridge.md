# Claude Code Bridge

Claude Code auto-loads `CLAUDE.md` from the project root at session start.

## Bootstrap

### Option 1: MCP server (static mode)
Configure Lex as an MCP server in Claude Code settings:

```json
{
  "mcpServers": {
    "lex": {
      "command": "lex",
      "args": ["serve"]
    }
  }
}
```

Rules are resolved via the `lexicon resolve` MCP tool.

### Option 2: Hook (hook mode)
Configure Lex as a prompt hook:

```json
{
  "hooks": {
    "user-prompt-submit": [
      {"command": "lex enrich --format text"}
    ]
  }
}
```

Rules are injected into every prompt automatically.

## Adaptations

- Claude Code reads CLAUDE.md as a single always-apply rule
- Memory files in `.claude/memory/` persist across sessions
- No native glob routing — use `lex enrich` with `--files` flag for context-aware injection
- MCP tool supports `language`, `files`, `keywords` params for context-aware resolution
