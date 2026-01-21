# The Agentic Shift

Ship features faster with AI-assisted development.

## Start Here

| I want to... | Go to |
|--------------|-------|
| **Get started in 5 minutes** | [Quick Start](QUICK_START.md) |
| **Know if this is right for me** | [Limitations](LIMITATIONS.md) |
| **Enterprise/compliance needs** | [Enterprise Guide](ENTERPRISE.md) |
| **Deep dive on the workflow** | [Full Roadmap](THE_AGENTIC_SHIFT_ROADMAP.md) |

## The Workflow (30-second version)

```
You write spec → Claude asks questions → Claude generates code → You approve
```

That's it. Everything else is optional.

## Quick Start

```bash
# Prerequisites: Claude Code CLI installed and authenticated
cd your-project
claude /init
# Add your tech stack to CLAUDE.md
claude "Here's my feature spec: [paste spec]. Ask questions, then implement."
```

## What's in This Repo

```
├── QUICK_START.md          # 5-minute setup (start here)
├── LIMITATIONS.md          # When NOT to use this
├── ENTERPRISE.md           # Compliance, security, large orgs
├── THE_AGENTIC_SHIFT_ROADMAP.md  # Full deep-dive guide
│
└── agentic-shift-skill/    # Interactive training (optional)
    ├── modules/
    │   ├── 01-foundation.md      # CLAUDE.md basics
    │   ├── 02-mcp-mastery.md     # GitHub, Figma, DB connections (optional)
    │   ├── 03-tda-cycle.md       # Core workflow (the important one)
    │   ├── 04-governance.md      # Git hooks (optional)
    │   └── 05-skill-creation.md  # Custom workflows (optional)
    └── templates/
        ├── CLAUDE.md.template
        ├── SPEC.md.template
        └── pre-commit.template
```

## Core vs Optional

| Component | Required? | When to Use |
|-----------|-----------|-------------|
| CLAUDE.md | Yes | Always—gives Claude context |
| Spec → Generate → Approve workflow | Yes | Every feature |
| MCP integrations (GitHub, Figma, etc.) | No | If you need external tool access |
| Pre-commit hooks | No | When you want automated security checks |
| Custom skills | No | When you have repeatable workflows |
| AI code review tools | No | When you want additional review layers |

## Requirements

- Claude Code CLI (`npm install -g @anthropic-ai/claude-code`)
- Authenticated (`claude login`)

## Install the Training Skill (Optional)

```bash
cp -r agentic-shift-skill ~/.claude/skills/agentic-shift-onboard
claude /onboard
```

## License

MIT
