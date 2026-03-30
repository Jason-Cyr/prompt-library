# Agent Setup — Identity, Memory & Personality

Templates for giving your OpenClaw agent persistent identity and memory across sessions.

These are the workspace files that make an agent feel like a teammate instead of a stateless chatbot. Drop them in your OpenClaw workspace directory (`~/.openclaw/workspace/`) and customize.

## Files

| File | Purpose |
|------|---------|
| [`AGENTS.md`](./AGENTS.md) | Operating instructions — how the agent behaves, safety rules, memory management |
| [`SOUL.md`](./SOUL.md) | Personality and voice — who the agent *is* |
| [`USER.md`](./USER.md) | Context about you — so the agent knows who it's helping |
| [`IDENTITY.md`](./IDENTITY.md) | Name, avatar, quick identity reference |

## How It Works

OpenClaw loads these files at the start of every session. The agent reads them, knows who it is, knows who you are, and picks up where it left off using memory files.

The key insight: **your agent wakes up fresh every session**. These files *are* its memory. Without them, every conversation starts from zero.

## Setup

```bash
# Copy templates to your workspace
cp AGENTS.md SOUL.md USER.md IDENTITY.md ~/.openclaw/workspace/

# Edit each file to match your setup
# Then restart OpenClaw
openclaw gateway restart
```

## Tips

- **SOUL.md** is the most impactful file. A good soul file is the difference between a generic assistant and one that feels like *yours*.
- **USER.md** doesn't need to be exhaustive — start with name, timezone, and communication preferences. Add more as you go.
- **AGENTS.md** sets the rules of engagement. The memory management section is critical — without it, your agent won't maintain continuity.
- Create a `memory/` directory in your workspace for daily notes. The agent writes here automatically.
