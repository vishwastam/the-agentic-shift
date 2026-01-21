# The Agentic Shift - Onboarding Skill

Interactive onboarding for Claude Code. Learn to work as an **Architect and Reviewer**.

## The Core Workflow

```
Specify → Context → Plan → Generate Tests → Generate Code → Validate
  [You]   [Claude]  [Interactive]  [Claude]    [Claude]       [You]
```

You define WHAT. Claude figures out HOW (with your approval at the Plan stage).

## Installation

```bash
cp -r agentic-shift-skill ~/.claude/skills/agentic-shift-onboard
claude /onboard
```

## Modules

| Module | Topic | Required? |
|--------|-------|-----------|
| 1 | **Foundation** - CLAUDE.md and context | Yes |
| 2 | **MCP** - External tool integration | Optional |
| 3 | **Spec-Driven Dev** - The core workflow | Yes |
| 4 | **Governance** - Git hooks | Optional |
| 5 | **Skills** - Custom workflows | Yes |

## Commands

```bash
claude /onboard              # Full tutorial
claude /onboard-foundation   # Module 1
claude /onboard-mcp          # Module 2 (optional)
claude /onboard-tda          # Module 3 (core)
claude /onboard-governance   # Module 4 (optional)
claude /onboard-skills       # Module 5
```

## Templates

- `SPEC.md.template` - Feature specification
- `CLAUDE.md.template` - Project context
- `pre-commit.template` - Security hook
- `pre-push.template` - Optional audit hook

## License

MIT
