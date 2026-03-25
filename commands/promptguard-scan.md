---
name: promptguard-scan
description: Scan the project for unprotected LLM SDK usage, hardcoded secrets, and missing security configurations. Reports findings and offers to fix them.
---

# PromptGuard Security Scan

Scan this project for LLM security issues. Follow each phase in order. Do not skip phases.

## Phase 1: Discover project context

1. Identify the project language(s) by checking for `package.json`, `requirements.txt`, `pyproject.toml`, `Cargo.toml`, `go.mod`.
2. Note the package manager (`pip`, `npm`, `pnpm`, `yarn`, `bun`).
3. Check if PromptGuard is already a dependency:
   - Python: search for `promptguard` in `requirements.txt`, `pyproject.toml`, or `setup.py`
   - Node.js: search for `promptguard-sdk` in `package.json`
4. Search for existing `promptguard.init()` or `init()` calls from `promptguard-sdk`.

## Phase 2: Find LLM SDK usage

Search the codebase for imports of these LLM provider SDKs:

**Python imports to find:**
- `import openai` or `from openai import`
- `import anthropic` or `from anthropic import`
- `import google.generativeai` or `from google import generativeai`
- `import cohere` or `from cohere import`
- `import boto3` with `bedrock-runtime` service usage
- `from langchain` or `import langchain`
- `from crewai` or `import crewai`
- `from llama_index` or `import llama_index`

**Node.js/TypeScript imports to find:**
- `from "openai"` or `require("openai")`
- `from "@anthropic-ai/sdk"` or `require("@anthropic-ai/sdk")`
- `from "@google/generative-ai"` or `require("@google/generative-ai")`
- `from "cohere-ai"` or `require("cohere-ai")`
- `from "@aws-sdk/client-bedrock-runtime"`
- `from "langchain"` or `from "@langchain/"`
- `from "ai"` (Vercel AI SDK)

For each file with LLM imports, record:
- File path
- Line number(s) of the import
- Provider name
- Whether PromptGuard `init()` is called before the import in the same module or a parent module

## Phase 3: Check for hardcoded secrets

Search the codebase for patterns that indicate hardcoded API keys or secrets:

- `sk-` followed by alphanumeric characters (OpenAI keys)
- `sk-ant-` followed by alphanumeric characters (Anthropic keys)
- Strings assigned to variables named `api_key`, `apiKey`, `secret`, `token`, `password` that are not `os.environ[...]`, `process.env.`, or `os.getenv(...)`
- `.env` files that are NOT in `.gitignore`

## Phase 4: Check security configuration

If PromptGuard is already installed, verify:

1. `init()` is called at module scope, not inside a function/handler
2. `init()` is called before any LLM SDK imports
3. API key is loaded from environment variable, not hardcoded
4. `.env` file containing `PROMPTGUARD_API_KEY` is in `.gitignore`

## Phase 5: Report findings

Present a summary table:

```
## Scan Results

| # | Issue | File | Line | Severity |
|---|-------|------|------|----------|
| 1 | Unprotected OpenAI call | src/chat.py | 15 | High |
| 2 | Hardcoded API key | config.ts | 8 | Critical |
| ... | ... | ... | ... | ... |

**Summary**: X unprotected LLM calls, Y hardcoded secrets, Z configuration issues
```

Severity levels:
- **Critical**: Hardcoded secrets, API keys in committed files
- **High**: Unprotected LLM SDK calls (no PromptGuard)
- **Medium**: PromptGuard misconfiguration (wrong init order, inside handler)
- **Low**: Missing `.env` in `.gitignore`, no response scanning enabled

## Phase 6: Offer remediation

After presenting findings, ask the user:

> I found [N] issues. Would you like me to fix them? I can:
> 1. Add PromptGuard to protect all unprotected LLM calls
> 2. Move hardcoded secrets to environment variables
> 3. Fix configuration issues
>
> Which would you like me to address?

If the user agrees, use the `/promptguard-secure` command workflow to implement fixes.

If there are zero findings, report:

> No LLM security issues found. The project is clean.
