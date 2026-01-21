# Agentic Shift - Onboarding Skill

Interactive training for AI-assisted development.

## Install

```bash
cp -r agentic-shift-skill ~/.claude/skills/agentic-shift-onboard
claude /onboard
```

## What You'll Learn

```
You write spec → Claude generates code → You approve
```

Takes 5-10 minutes to complete your first feature.

## Commands

| Command | What it does |
|---------|--------------|
| `/onboard` | Full tutorial (recommended) |
| `/onboard-foundation` | Just CLAUDE.md setup |
| `/onboard-tda` | Just the core workflow |

## Optional Add-ons

Only install these when you need them:

| Command | When to use |
|---------|-------------|
| `/onboard-mcp` | Need GitHub, Figma, or database access |
| `/onboard-governance` | Want automated security checks |
| `/onboard-skills` | Have repeatable workflows to automate |

## Templates

```
templates/
├── CLAUDE.md.template   # Project context (required)
├── SPEC.md.template     # Feature specs (optional)
└── pre-commit.template  # Security hook (optional)
```
