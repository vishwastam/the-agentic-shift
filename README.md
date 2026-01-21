# The Agentic Shift

A comprehensive framework for transitioning software teams from manual coding to agent-led development with Claude Code.

## Overview

The Agentic Shift transforms developers from manual coders into **Architects and Reviewers**—professionals who define intent, establish constraints, and validate outcomes while Claude Code handles implementation details.

## What's Included

### 1. Strategic Roadmap

[`THE_AGENTIC_SHIFT_ROADMAP.md`](./THE_AGENTIC_SHIFT_ROADMAP.md)

A complete guide for team leads and engineering managers covering:
- 4-phase rollout plan (12 weeks)
- Environment setup and security guardrails
- MCP (Model Context Protocol) integration
- The Architect-Reviewer workflow model
- Test-Driven Agentic (TDA) development cycle
- Governance and pre-commit/pre-push hooks
- Metrics and KPIs for measuring success

### 2. Interactive Onboarding Skill

[`agentic-shift-skill/`](./agentic-shift-skill/)

An executable Claude Code skill that provides hands-on training:

| Module | Topic | What You'll Learn |
|--------|-------|-------------------|
| 1 | Foundation | Creating effective CLAUDE.md project context |
| 2 | MCP Mastery | Integrating GitHub, Postgres, Slack via MCP |
| 3 | TDA Cycle | Test-driven agentic development workflow |
| 4 | Governance | Automated security scanning with git hooks |
| 5 | Skill Creation | Building reusable workflow automations |

## Quick Start

### Install the Onboarding Skill

```bash
# Clone this repository
git clone https://github.com/vishwastam/the-agentic-shift.git

# Copy the skill to your Claude Code skills directory
cp -r the-agentic-shift/agentic-shift-skill ~/.claude/skills/agentic-shift-onboard

# Start the interactive onboarding
claude /onboard
```

### Available Commands

After installation, these commands become available in Claude Code:

| Command | Description |
|---------|-------------|
| `/onboard` | Start the full interactive tutorial (~90 min) |
| `/onboard-foundation` | Jump to Module 1: CLAUDE.md creation |
| `/onboard-mcp` | Jump to Module 2: MCP integration |
| `/onboard-tda` | Jump to Module 3: Test-driven agentic |
| `/onboard-governance` | Jump to Module 4: Git hooks |
| `/onboard-skills` | Jump to Module 5: Custom skill creation |

## The New Developer Workflow

```
┌─────────────────────────────────────────────────────────────────┐
│                    AGENTIC WORKFLOW                              │
│                                                                  │
│  ARCHITECT PHASE          AGENT PHASE           REVIEW PHASE    │
│  ┌──────────────┐        ┌──────────────┐      ┌──────────────┐ │
│  │ Define Intent│   →    │ Claude Code  │  →   │ Validate &   │ │
│  │ Set Constraints│      │ Implements   │      │ Refine       │ │
│  │ Write Tests  │        │ Iterates     │      │ Approve      │ │
│  └──────────────┘        └──────────────┘      └──────────────┘ │
│      [Human]                 [Agent]               [Human]       │
└─────────────────────────────────────────────────────────────────┘
```

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/claude-code) installed and authenticated
- Git
- A project to work with (or willingness to create a practice project)

## Templates Included

The skill includes ready-to-use templates:

- **CLAUDE.md template** - Starter context file for any project
- **pre-commit hook** - Fast security scanning before commits
- **pre-push hook** - Comprehensive audit before pushing

## Contributing

Contributions are welcome! Feel free to:
- Submit issues for bugs or feature requests
- Fork and submit pull requests
- Share your custom skills built with this framework

## License

MIT License - Feel free to use, modify, and distribute.

---

**Ready to transform how your team builds software?**

```bash
claude /onboard
```
