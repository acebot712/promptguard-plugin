# AGENTS.md

## Overview

Cursor plugin for PromptGuard. Provides rules, skills, slash commands, and an MCP server configuration for AI-assisted LLM security in Cursor IDE.

This repo contains static configuration files (Markdown, JSON) with no build step or automated tests.

## Repository Layout

```
.cursor-plugin/
└── plugin.json        # Plugin manifest

rules/                 # Cursor rules (.mdc files)
├── secure-llm-usage.mdc
└── llm-security-review.mdc

skills/                # Agent skills (SKILL.md format)
└── secure-llm-integration/
    ├── SKILL.md
    └── references/

commands/              # Slash commands
├── promptguard-scan.md
└── promptguard-secure.md

agents/                # Agent definitions
└── llm-security-reviewer.md

mcp.json               # MCP server registration (points to promptguard CLI)
```

## How It Works

The plugin connects to the PromptGuard CLI via MCP (`promptguard mcp -t stdio`). Rules and skills guide Cursor's AI to follow LLM security best practices.

## Development

No build step. To test changes:

1. Open this repo in Cursor
2. Verify rules appear in Cursor's rules panel
3. Test slash commands work as expected
4. Verify MCP server connects (requires `promptguard` CLI on PATH)

## Coding Standards

- Rules use `.mdc` format (Cursor rule syntax)
- Skills follow `SKILL.md` format with clear trigger conditions
- JSON files must be valid (validate before committing)
- Markdown should be clear and actionable for AI agents

## Boundaries

### Never
- Commit API keys, tokens, or credentials
- Reference internal/private repository paths or infrastructure details
- Add binary files or large assets
