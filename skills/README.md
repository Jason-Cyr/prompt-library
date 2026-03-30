# Skills

Drop-in OpenClaw skills — self-contained capability modules your agent can use.

## What's a Skill?

An OpenClaw skill is a `SKILL.md` file that teaches your agent how to do something specific. Drop it in `~/.openclaw/workspace/skills/` and the agent picks it up automatically.

Skills are different from prompts: they're persistent capabilities, not one-shot instructions.

## Available Skills

| Skill | Description |
|-------|-------------|
| [`vault-search/`](./vault-search/) | Search an Obsidian vault using semantic + keyword search (via qmd) |

## Skill Format

Every skill needs a `SKILL.md` with:

```markdown
# Skill Name

## Description
What it does, when to use it.

## Prerequisites
What needs to be installed/configured.

## Usage
How the agent should use it, with examples.
```

See the [OpenClaw docs](https://docs.openclaw.ai) for the full skill specification.
