# Contributing to PromptGuard Cursor Plugin

## Overview

This repository is a Cursor plugin -- a collection of static configuration files that Cursor loads to provide PromptGuard capabilities. There is no build step, no compiled code, and no test suite.

## Prerequisites

| Tool | Version |
|------|---------|
| Cursor | latest |
| PromptGuard CLI | latest (`brew install promptguard/tap/promptguard`) |
| PromptGuard API key | [app.promptguard.co/settings/api-keys](https://app.promptguard.co/settings/api-keys) |

## Plugin Structure

```
.cursor-plugin/
  plugin.json               # Plugin manifest (name, version, description, logo)

rules/
  secure-llm-usage.mdc      # Always-on rule: guides the agent to use PromptGuard
  llm-security-review.mdc   # Security review rule

skills/
  secure-llm-integration/
    SKILL.md                 # Step-by-step integration playbook
    references/
      threat-model.md        # LLM threat model reference

commands/
  promptguard-scan.md        # /promptguard-scan command definition
  promptguard-secure.md      # /promptguard-secure command definition

agents/
  llm-security-reviewer.md   # LLM Security Reviewer agent definition

mcp.json                     # MCP server configuration (promptguard mcp -t stdio)
```

### Key Components

| Component | File(s) | Purpose |
|---|---|---|
| **Plugin manifest** | `.cursor-plugin/plugin.json` | Metadata: name, version, description, logo |
| **Rules** | `rules/*.mdc` | Always-on guidance injected into agent context |
| **Skills** | `skills/*/SKILL.md` | Step-by-step playbooks the agent follows on request |
| **Commands** | `commands/*.md` | Slash commands (`/promptguard-scan`, `/promptguard-secure`) |
| **Agents** | `agents/*.md` | Specialized agent definitions |
| **MCP config** | `mcp.json` | Registers the PromptGuard MCP server |

## Development Workflow

### Making Changes

1. Edit the relevant `.md`, `.mdc`, or `.json` file
2. Test in Cursor (see below)
3. Commit and push to `main`

### Testing

Testing is manual. There is no automated test suite for Cursor plugins.

**To test locally:**

1. Clone or symlink this repo into your Cursor plugins directory, or open a project that references it
2. Restart Cursor to pick up changes
3. Verify each component:

| Component | How to verify |
|---|---|
| **Rules** | Open a file with LLM imports (e.g., `import openai`). The agent should suggest PromptGuard when writing LLM code. |
| **Skills** | Ask the agent: "Add PromptGuard to this project". It should follow the skill playbook. |
| **Commands** | Type `/promptguard-scan` or `/promptguard-secure` in the agent chat. |
| **MCP tools** | Open MCP panel in Cursor settings. Verify `promptguard` server is listed and tools appear (scan_text, redact, etc.). |
| **Agents** | Select the "LLM Security Reviewer" agent and ask it to review code. |

### MCP Server Testing

The MCP server is provided by the PromptGuard CLI, not by this plugin. The plugin only configures Cursor to use it via `mcp.json`. To test:

```bash
promptguard mcp -t stdio        # Verify CLI MCP server starts
promptguard --version            # Verify CLI is installed
```

## CI/CD

There is no CI pipeline. The repository has Dependabot configured for GitHub Actions updates (`.github/dependabot.yml`).

## Publishing

Changes are published by pushing to the `main` branch. Cursor pulls plugin updates from the repository directly. There is no build, packaging, or registry publishing step.

To bump the plugin version, update `version` in `.cursor-plugin/plugin.json`.

## PR Checklist

- [ ] `.cursor-plugin/plugin.json` is valid JSON
- [ ] `mcp.json` is valid JSON
- [ ] Rules, skills, commands, and agents render correctly in markdown
- [ ] Manually tested in Cursor
- [ ] PR description explains the change

## Reporting Issues

Open an issue at https://github.com/acebot712/promptguard-plugin/issues with:

- Cursor version
- PromptGuard CLI version (`promptguard --version`)
- What you expected vs. what happened
- Screenshots if applicable
