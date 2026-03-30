# DefenseClaw — Install DefenseClaw on Your OpenClaw Agent

This prompt will set up [DefenseClaw](https://github.com/Jason-Cyr/OpenClawSecurity) — an independent security layer that scans every message flowing in and out of your OpenClaw agent for threats like prompt injection, PII leaks, exposed API keys, and more.

## Before You Start

**⚠️ Important: Do NOT give this prompt to OpenClaw itself.**

Use [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [Codex](https://openai.com/index/codex/), or any external coding agent to run this install. Here's why: DefenseClaw modifies OpenClaw's configuration and restarts the gateway. If something goes wrong during install, OpenClaw can't start — and if it can't start, it can't debug itself. You'd be stuck. An external coding agent can troubleshoot and fix things even when OpenClaw is down.

## What You'll Need

- **OpenClaw** installed and running ([openclaw.ai](https://openclaw.ai))
- **Go 1.24+** installed (the scanner is written in Go)
- **Git** installed
- A coding agent that runs outside of OpenClaw (Claude Code, Codex, Cursor, etc.)

Don't know if you have Go? Open a terminal and run `go version`. If you get "command not found," install it from [go.dev/dl](https://go.dev/dl/) first.

## The Prompt

Copy everything below and paste it into your coding agent (Claude Code, Codex, etc.):

---

```
I need you to install DefenseClaw, an independent security scanning layer for
OpenClaw. Follow these steps carefully and in order. Stop and tell me if
anything fails — don't try to work around errors silently.

STEP 1 — CHECK DEPENDENCIES

Before doing anything else, verify these are installed:
  - Go 1.24 or higher: run `go version`
  - Git: run `git version`
  - OpenClaw: run `openclaw status`

If any of these are missing, stop and tell me what needs to be installed.
Do not proceed without all three.

STEP 2 — CLONE THE REPO

Clone the DefenseClaw repository:

  git clone https://github.com/Jason-Cyr/OpenClawSecurity.git
  cd OpenClawSecurity

STEP 3 — BUILD

Build all binaries. This requires CGO support (enabled by default on most
systems):

  CGO_ENABLED=1 make build

You should see binaries created in the `bin/` directory:
  - clawshield-scan (the content scanner)
  - clawshield-fw (the egress firewall)
  - clawshield-proxy (optional HTTP proxy)
  - clawshield-setup (setup wizard)

If the build fails with CGO errors, you may need to install a C compiler:
  - macOS: run `xcode-select --install`
  - Ubuntu/Debian: run `sudo apt install build-essential`
  - Then retry the build.

STEP 4 — RUN THE TESTS

Make sure everything built correctly:

  CGO_ENABLED=1 make test

All tests should pass. If any fail, stop and show me the output.

STEP 5 — INSTALL THE OPENCLAW PLUGIN

Copy the plugin files into OpenClaw's extensions directory:

  mkdir -p ~/.openclaw/extensions/clawshield
  cp extensions/clawshield/index.ts ~/.openclaw/extensions/clawshield/
  cp extensions/clawshield/package.json ~/.openclaw/extensions/clawshield/
  cp extensions/clawshield/openclaw.plugin.json ~/.openclaw/extensions/clawshield/

STEP 6 — CONFIGURE OPENCLAW

Read the current OpenClaw config:

  cat ~/.openclaw/openclaw.json

Add the DefenseClaw plugin to the `plugins.entries` section. You need to use
the FULL ABSOLUTE PATHS to where you cloned the repo. For example, if you
cloned to /home/user/OpenClawSecurity, the config would be:

  {
    "plugins": {
      "entries": {
        "clawshield": {
          "enabled": true,
          "config": {
            "policyPath": "/home/user/OpenClawSecurity/config/production.yaml",
            "scannerBinary": "/home/user/OpenClawSecurity/bin/clawshield-scan",
            "auditLog": "/home/user/OpenClawSecurity/clawshield-audit.jsonl",
            "mode": "production",
            "scanInbound": true,
            "scanOutbound": true
          }
        }
      }
    }
  }

IMPORTANT:
  - Replace the paths with the ACTUAL location where you cloned the repo
  - If openclaw.json already has a plugins section, merge into it — don't
    overwrite existing plugins
  - Make sure the JSON is valid after editing (no trailing commas, proper nesting)

STEP 7 — CUSTOMIZE THE DOMAIN ALLOWLIST

Open config/production.yaml and review the `domains` section at the bottom.
These are the domains the scanner recognizes as allowed. Add any API domains
your agent needs to reach. Common additions:
  - Your LLM provider's API (api.anthropic.com, api.openai.com, etc.)
  - Any webhook endpoints you use
  - Tool APIs (GitHub, Linear, Slack, etc.)

STEP 8 — TEST THE SCANNER MANUALLY

Before restarting OpenClaw, verify the scanner works on its own:

  echo '{"content":"This is a normal message"}' | ./bin/clawshield-scan --policy config/production.yaml 2>/dev/null

Should return: {"decision":"allow",...}

  echo '{"content":"AKIAIOSFODNN7EXAMPLE"}' | ./bin/clawshield-scan --policy config/production.yaml 2>/dev/null

Should return: {"decision":"deny",...} (detected an AWS key pattern)

If either test gives unexpected results, stop and show me the output.

STEP 9 — RESTART OPENCLAW

Now restart the gateway to load the plugin:

  openclaw gateway restart

Then check it started successfully:

  openclaw gateway status

If it fails to start, check the logs. Common issues:
  - Invalid JSON in openclaw.json (fix the syntax)
  - Wrong file paths in the plugin config (verify paths exist)
  - Missing binary (re-run make build)

Do NOT proceed until OpenClaw is running successfully.

STEP 10 — VERIFY IT'S WORKING

Send a test message through OpenClaw and check the audit log:

  tail -5 /path/to/OpenClawSecurity/clawshield-audit.jsonl

You should see scan entries appearing. The "decision" field will be "allow"
for clean messages.

That's it! DefenseClaw is now scanning every message in and out of your agent.

Give me a summary of what was installed, any issues encountered, and confirm
the final status of OpenClaw.
```

---

## What This Does

Once installed, DefenseClaw sits between the outside world and your agent:

- **Inbound messages** are scanned before your agent sees them
- **Outbound responses** are scanned before they're sent
- Threats are **blocked** (injection, secrets, vulnerabilities, malware) or **redacted** (PII)
- Every scan decision is logged for audit

Your agent doesn't know it's there. It just works.

## What It Catches

| Threat | Action | Examples |
|--------|--------|----------|
| Prompt Injection | Block | Hidden instructions, role hijacking, Unicode tricks |
| PII | Redact | Credit cards, SSNs, phone numbers, emails |
| Secrets | Block | AWS keys, GitHub tokens, API keys, private keys |
| Vulnerabilities | Block | SQL injection, SSRF, path traversal, XSS |
| Malware Indicators | Block | Reverse shells, cryptominers, suspicious binaries |

## Troubleshooting

**OpenClaw won't start after install:**
The most common cause is invalid JSON in `openclaw.json`. Open it in a JSON validator and fix any syntax errors. Then run `openclaw gateway restart`.

**Scanner returns errors:**
Make sure the binary path in `openclaw.json` points to the actual `clawshield-scan` binary. Run it directly to test: `echo '{"content":"test"}' | /full/path/to/bin/clawshield-scan --policy /full/path/to/config/production.yaml`

**False positives:**
If DefenseClaw is blocking legitimate messages, you can switch to dev mode first (`config/dev_default.yaml`) which logs but doesn't block. Review the audit log to tune your policy, then switch back to production.

## The Code

Full source code: **[github.com/Jason-Cyr/OpenClawSecurity](https://github.com/Jason-Cyr/OpenClawSecurity)**
