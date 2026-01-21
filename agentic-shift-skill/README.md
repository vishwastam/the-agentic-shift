# The Agentic Shift - Onboarding Skill

Interactive onboarding for Claude Code. Learn to work as an **Architect and Reviewer**.

## Installation

```bash
# Copy to your Claude Code skills directory
cp -r agentic-shift-skill ~/.claude/skills/agentic-shift-onboard

# Start the onboarding
claude /onboard
```

## Modules

| Module | Topic | Required? |
|--------|-------|-----------|
| 1 | **Foundation** - CLAUDE.md and context | Yes |
| 2 | **MCP** - External tool integration | Optional |
| 3 | **TDA Cycle** - Test-driven development | Yes (core) |
| 4 | **Governance** - Git hooks | Optional |
| 5 | **Skills** - Create custom workflows | Yes |

## Commands

```bash
claude /onboard              # Full tutorial
claude /onboard-foundation   # Module 1 only
claude /onboard-mcp          # Module 2 only
claude /onboard-tda          # Module 3 only
claude /onboard-governance   # Module 4 only
claude /onboard-skills       # Module 5 only
```

## Templates Included

- `templates/CLAUDE.md.template` - Starter context file
- `templates/pre-commit.template` - Basic security hook
- `templates/pre-push.template` - Optional Claude-powered audit

## The Core Workflow

```
Write tests → Prompt Claude → Run tests → Iterate
```

That's it. Everything else supports this loop.

## License

MIT
