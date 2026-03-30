# DefenseClaw — The Build Prompt

This is the prompt I gave my AI agent to build ClawShield, the independent security scanning layer for OpenClaw. One prompt produced the full working system.

## Context

I had an existing Go security proxy project targeting Claude Desktop. I asked my agent to retarget it for OpenClaw and expand it into a complete two-layer security system.

## The Prompt

```
Clone the repo at github.com/Jason-Cyr/OpenClawSecurity (currently just a zip file).
Refactor it to target OpenClaw instead of Claude Desktop.

Build a two-layer, defense-in-depth security system:

LAYER 1 — APPLICATION CONTENT SCANNER:
- Create a standalone Go binary (clawshield-scan) that reads JSON from stdin
  and outputs scan results to stdout
- Implement these scanner modules:
  - Prompt Injection: regex patterns + structural analysis (base64 decode,
    imperative density scoring, Shannon entropy) + Unicode evasion detection
    (zero-width chars, tag characters U+E0000–U+E007F, NFKC normalization,
    homoglyphs)
  - PII Detection: credit cards with Luhn validation, SSNs, emails, phone
    numbers, IP addresses, dates of birth, passport numbers, bank accounts,
    medical IDs — each category independently configurable (block or redact)
  - Secrets Scanning: API key patterns for AWS, GCP, Azure, GitHub, GitLab,
    Slack, Stripe, Twilio, SendGrid, npm, PyPI + JWTs, private keys,
    database connection strings, generic high-entropy patterns
  - Vulnerability Detection: SQL injection (UNION, tautology, blind), SSRF
    (cloud metadata endpoints, private IP ranges, alternate encodings),
    path traversal, command injection, XSS
  - Malware Indicators: binary magic bytes (PE, ELF, Mach-O, JAR), reverse
    shell patterns, cryptominer indicators, C2 beacon patterns, high-entropy
    payload detection, zip bomb detection
- Policy engine: YAML-based config files with dev (allow-by-default, log only)
  and production (deny-by-default, block/redact) modes
- Hot-reloadable policies — edit the YAML, changes apply without restart

Create an OpenClaw TypeScript plugin (extensions/clawshield/):
- Hook into before_agent_start (inbound scanning) and agent_end (outbound scanning)
- Pipe message content to the Go scanner binary via stdin/stdout
- Strip OpenClaw envelope metadata before scanning to reduce false positives
  (JSON blocks, system timestamps, sender metadata)
- Deduplicate findings
- Write a JSONL audit log of every scan decision

LAYER 2 — EGRESS FIREWALL:
- Create clawshield-fw binary
- YAML config with domain and port allowlists
- Deny-by-default: only explicitly listed domains can be reached
- Platform abstraction: pfctl backend for macOS, iptables backend for Linux
- DNS resolution of domain names to IP addresses for rule generation
- Input validation: block shell injection in all firewall arguments
- Clean shutdown: automatically remove all firewall rules on exit or Ctrl+C
- Dry-run mode to preview generated rules without applying them

AUDIT & COMPLIANCE:
- JSONL audit trail with timestamp, direction, findings, scan latency
- SQLite database with batch writing (100 records / 5s flush)
- Integrity checksums (SHA-256 every 100/1000 entries) with JSONL fallback on DB errors
- AES-256-GCM field encryption for sensitive audit data
- SIEM forwarding: syslog over TLS (RFC 5424, mutual auth) + HTTPS webhook
  (OCSF v1.1 format)

ADAPTIVE THREAT RESPONSE:
- Event bus tracking threat scores per source
- Configurable thresholds: score 30+ → elevate sensitivity, 50+ → restrict
  domains, 70+ → deny-by-default, 90+ → block session
- Score decay: 1 point per 30 seconds of inactivity
- Rate limiting on redundant triggers

Write comprehensive tests. Target full coverage of all scanner modules,
policy logic, firewall rule compilation, and input validation.

Make it production-ready: proper error handling, clean logging, <1ms scan
latency per message.
```

## What It Produced

- `bin/clawshield-scan` — Standalone Go scanner binary
- `bin/clawshield-fw` — Egress firewall with pfctl/iptables backends
- `bin/clawshield-proxy` — HTTP/WebSocket reverse proxy (optional)
- `bin/clawshield-setup` — Interactive setup wizard
- `extensions/clawshield/` — OpenClaw TypeScript plugin
- `config/production.yaml` — Production policy (block + redact)
- `config/dev_default.yaml` — Dev policy (log only)
- `firewall/config/egress.yaml` — Domain allowlist
- 59 passing tests with race detection

## Notes

- This was built by an AI agent (Claude, running inside OpenClaw via [OpenClaw](https://github.com/openclaw/openclaw))
- The full source code is at [github.com/Jason-Cyr/OpenClawSecurity](https://github.com/Jason-Cyr/OpenClawSecurity)
- You'll need Go 1.24+ with CGO support for SQLite
- The production policy in this repo is a sanitized version — customize the domain allowlist for your setup
