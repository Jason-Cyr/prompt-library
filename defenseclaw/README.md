# DefenseClaw — Security Governance for OpenClaw Agents

> 📺 Video: [Your AI Agent Has a Massive Security Problem (Here's the Fix)](https://youtube.com/@Jason_Cyr)

[DefenseClaw](https://github.com/cisco-ai-defense/defenseclaw) is an open-source security governance layer for [OpenClaw](https://github.com/nvidia/openclaw) AI agents. It scans every skill, MCP server, plugin, and tool call before allowing it to run — and blocks anything dangerous automatically.

Built by the [Cisco AI Defense](https://www.cisco.com/site/us/en/products/security/ai-defense/index.html) team.

## What It Does

- **Skill Scanning** — Scans every OpenClaw skill before it runs. HIGH/CRITICAL findings are auto-blocked.
- **MCP Server Scanning** — Inspects MCP servers for injection, data exfiltration, and unsafe patterns.
- **CodeGuard** — Static analysis engine that catches hardcoded credentials, dangerous execution, SQL injection, path traversal, and more in agent-generated code.
- **LLM Guardrail** — Proxy that inspects every prompt and completion for secrets, PII, and injection attacks.
- **Tool Inspection** — Every tool call passes through the inspect engine before execution, checking for secrets, dangerous commands, sensitive paths, C2 patterns, cognitive file tampering, and prompt injection.
- **Audit & SIEM** — SQLite audit store with Splunk forwarding, OTEL export, and full compliance trail.

## The Prompt

The prompt below will get a coding agent (Claude Code, Codex, etc.) to install DefenseClaw on your OpenClaw setup. It checks dependencies, installs everything, and verifies it's working.

👉 **[PROMPT.md](./PROMPT.md)**

## The Code

👉 **[github.com/cisco-ai-defense/defenseclaw](https://github.com/cisco-ai-defense/defenseclaw)**
