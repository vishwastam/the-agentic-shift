# The Agentic Shift

Transform your team from manual coders into **Architects and Reviewers** with Claude Code.

## What's Included

### 1. Strategic Roadmap

[`THE_AGENTIC_SHIFT_ROADMAP.md`](./THE_AGENTIC_SHIFT_ROADMAP.md)

A self-paced guide covering:
- Environment setup and security
- MCP integration (optional)
- The Test-Driven Agentic workflow
- Governance with git hooks

### 2. Interactive Onboarding Skill

[`agentic-shift-skill/`](./agentic-shift-skill/)

Hands-on training with 5 modules:

| Module | Topic | Required? |
|--------|-------|-----------|
| Foundation | CLAUDE.md and context | Yes |
| MCP | External tool integration | Optional |
| TDA Cycle | Test-driven development | Yes (core) |
| Governance | Git hooks | Optional |
| Skills | Custom workflows | Yes |

## Quick Start

```bash
# Clone this repo
git clone https://github.com/vishwastam/the-agentic-shift.git

# Install the skill
cp -r the-agentic-shift/agentic-shift-skill ~/.claude/skills/agentic-shift-onboard

# Start onboarding
claude /onboard
```

## The Core Workflow

```
Write tests → Prompt Claude → Run tests → Iterate
```

This is the Test-Driven Agentic (TDA) cycle. Everything else supports this pattern.

## Requirements

- Claude Code CLI installed and authenticated
- A project to work with

## License

MIT
