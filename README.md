# PromptGuard Plugin for Cursor

Protect LLM-powered applications from prompt injection, PII leakage, and data exfiltration -- directly from your editor.

## Quick install

**One-click install** (opens Cursor and installs the plugin + MCP server):

```
cursor://anysphere.cursor-deeplink/mcp/install?name=promptguard&config=eyJjb21tYW5kIjoicHJvbXB0Z3VhcmQiLCJhcmdzIjpbIm1jcCIsIi10Iiwic3RkaW8iXX0=
```

Or manually: Cursor Settings > Plugins > search "promptguard" > Install.

## What's included

| Component | Description |
|-----------|-------------|
| **MCP Server** | Native MCP server (`promptguard mcp`) exposes 6 tools: auth, logout, scan text for threats, scan projects for unprotected LLM usage, redact PII, and check status. Works with Cursor, Claude Code, Windsurf, and any MCP-compatible client. |
| **Rules** | Always-on guidance that ensures the Cursor agent writes secure LLM code. When you use OpenAI, Anthropic, or any supported provider, the agent knows to include PromptGuard. |
| **Skill** | Step-by-step integration playbook. The agent uses this to add PromptGuard to any project -- detecting the language, choosing the right method, and configuring everything correctly. |
| **Commands** | `/promptguard-scan` finds unprotected LLM calls and hardcoded secrets. `/promptguard-secure` adds PromptGuard to the project. |
| **Agent** | LLM Security Reviewer -- a specialized code reviewer that finds prompt injection vectors, PII leakage, agent tool abuse, and other LLM-specific vulnerabilities. |

## How it works

### Always-on rule: Secure LLM Usage

When the Cursor agent writes code that imports OpenAI, Anthropic, Google AI, Cohere, Bedrock, LangChain, CrewAI, or Vercel AI SDK, the rule guides it to include `promptguard.init()` with proper configuration. No manual invocation needed.

### Skill: Secure LLM Integration

Activated when you ask the agent to add PromptGuard or build AI features. The skill walks through:

1. Detecting the project language and LLM providers
2. Choosing the right integration method (auto-instrumentation, Guard API, or HTTP proxy)
3. Installing the SDK and adding initialization
4. Configuring security settings
5. Verifying the setup works

Includes a full [LLM threat model reference](skills/secure-llm-integration/references/threat-model.md) covering prompt injection, PII leakage, data exfiltration, agent tool abuse, and more.

### Command: `/promptguard-scan`

Scans the project for:

- Unprotected LLM SDK calls (missing PromptGuard)
- Hardcoded API keys and secrets
- Misconfigured PromptGuard initialization
- Missing `.env` in `.gitignore`

Reports findings in a severity-ranked table and offers to fix them.

### Command: `/promptguard-secure`

Adds PromptGuard to the project end-to-end:

- Detects language, package manager, and entry points
- Installs `promptguard-sdk`
- Adds `init()` at the correct location (before LLM imports)
- Sets up environment variables
- Handles framework-specific patterns (Next.js, FastAPI, Lambda)

### Agent: LLM Security Reviewer

A specialized security reviewer that focuses exclusively on LLM application threats:

- Prompt injection (direct and indirect)
- PII leakage in prompts and responses
- Agent and tool security (SQL injection via tools, SSRF, path traversal)
- Secrets exposure in LLM context
- Unsafe output handling (XSS via LLM responses)

Only flags real, exploitable vulnerabilities with concrete attack scenarios and fixes. No noise.

## MCP Server

The plugin includes a native MCP server built into the PromptGuard CLI. When installed, Cursor (and other AI editors) can call these tools directly:

| Tool | Description |
|------|-------------|
| `promptguard_auth` | Authenticate with PromptGuard. Opens the dashboard in the browser so the user can copy their API key, then saves it locally. |
| `promptguard_logout` | Log out by removing the locally stored API key and configuration. |
| `promptguard_scan_text` | Scan any text for prompt injection, jailbreaks, PII leakage, and toxic content. Returns decision, confidence, and threat details. |
| `promptguard_scan_project` | Scan a project directory for unprotected LLM SDK usage across all supported providers. |
| `promptguard_redact` | Redact PII (emails, phones, SSNs, credit cards, API keys) from text. Returns sanitized output. |
| `promptguard_status` | Show current PromptGuard configuration, active providers, and key type. |

**Manual MCP setup** (if not using the plugin):

Add to your `.cursor/mcp.json` or global MCP config:

```json
{
  "mcpServers": {
    "promptguard": {
      "command": "promptguard",
      "args": ["mcp", "-t", "stdio"]
    }
  }
}
```

This works with any MCP-compatible client: Cursor, Claude Code, Windsurf, Zed, etc.

## Prerequisites

- **PromptGuard CLI** -- required for MCP server:
  - macOS: `brew install promptguard/tap/promptguard`
  - Linux/macOS: `curl -fsSL https://raw.githubusercontent.com/acebot712/promptguard-cli/main/install.sh | sh`
  - Cargo: `cargo install promptguard-cli`
- **PromptGuard account** -- sign up at [promptguard.co](https://promptguard.co)
- **API key** -- get one from [app.promptguard.co/settings/api-keys](https://app.promptguard.co/settings/api-keys)
- **PromptGuard SDK** (installed per-project):
  - Python: `pip install promptguard-sdk`
  - Node.js: `npm install promptguard-sdk`

## Supported LLM providers

OpenAI, Anthropic, Google Generative AI (Gemini), Cohere, AWS Bedrock, LangChain, CrewAI, LlamaIndex, Vercel AI SDK.

## Links

- [Documentation](https://docs.promptguard.co)
- [Dashboard](https://app.promptguard.co)
- [Python SDK](https://github.com/acebot712/promptguard-python)
- [Node.js SDK](https://github.com/acebot712/promptguard-node)
- [Website](https://promptguard.co)
