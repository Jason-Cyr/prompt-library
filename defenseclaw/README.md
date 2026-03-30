# DefenseClaw — Security Scanning for OpenClaw

> 📺 Video: [Your AI Agent Has a Massive Security Problem (Here's the Fix)](https://youtube.com/@Jason_Cyr)

DefenseClaw is an independent, two-layer security system that sits between the outside world and your AI agent. It scans every inbound and outbound message for threats — prompt injection, PII leaks, exposed secrets, SQL injection, malware indicators — and blocks or redacts them before they reach (or leave) your agent.

## What It Catches

- **Prompt Injection** — Pattern matching, structural analysis, Unicode evasion detection
- **PII** — Credit cards (Luhn validated), SSNs, emails, phone numbers, IPs, passports, and more
- **Secrets** — API keys for AWS, GCP, Azure, GitHub, Slack, Stripe, and 7+ other providers
- **Vulnerabilities** — SQLi, SSRF, path traversal, command injection, XSS
- **Malware Indicators** — Binary magic bytes, reverse shells, cryptominers, high-entropy payloads

Plus an egress firewall (Layer 2) that controls which domains your agent can reach — deny-by-default.

## The Prompt

The prompt below is what I gave my AI agent (Gibson) to build DefenseClaw from scratch. It produced the full working system: Go scanner binary, OpenClaw plugin, egress firewall, policy engine, audit logging, and test suite.

👉 **[PROMPT.md](./PROMPT.md)**

## The Code

The full open-source DefenseClaw codebase is here:

👉 **[github.com/Jason-Cyr/OpenClawSecurity](https://github.com/Jason-Cyr/OpenClawSecurity)**

## Setup

After cloning the DefenseClaw repo, the basic setup is:

```bash
# Build
cd OpenClawSecurity
make build

# Install the OpenClaw plugin
make install-plugin

# Configure (add to ~/.openclaw/openclaw.json)
# See the full README in the DefenseClaw repo for config details

# Restart OpenClaw
openclaw gateway restart
```

## Policy Configuration

DefenseClaw uses YAML policy files. Here's the production policy I run:

👉 **[production-policy.yaml](./production-policy.yaml)** — Block injection/secrets/vulns/malware, redact PII, domain allowlist enforced.

You'll want to customize the `domains` list for your own setup (which APIs does your agent need to reach?).
