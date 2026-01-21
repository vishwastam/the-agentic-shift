# The Agentic Shift

Ship features faster with AI-assisted development.

## Install

```bash
curl -sL https://raw.githubusercontent.com/vishwastam/the-agentic-shift/main/install.sh | bash
```

## Use

```bash
cd your-project
claude /agentic-shift
```

The setup is interactive. It will:
1. Ask about your project and context
2. Create a minimal CLAUDE.md
3. Walk you through your first AI-assisted feature

Takes about 5 minutes.

## What It Does

```
You write spec → Claude asks questions → Claude generates code → You approve
```

That's the core workflow. Optional add-ons (GitHub integration, Figma, security hooks) are offered based on your needs.

## Requirements

- [Claude Code CLI](https://docs.anthropic.com/claude-code) installed
- Authenticated (`claude login`)

## Manual Install

If you prefer not to use the install script:

```bash
git clone https://github.com/vishwastam/the-agentic-shift.git
cp -r the-agentic-shift/agentic-shift-skill ~/.claude/skills/agentic-shift
```

## Documentation

The skill is self-contained and interactive—you don't need to read docs to get started.

For reference:
- [LIMITATIONS.md](LIMITATIONS.md) — When AI-assisted dev isn't the right fit
- [ENTERPRISE.md](ENTERPRISE.md) — Compliance, security, large organizations
- [THE_AGENTIC_SHIFT_ROADMAP.md](THE_AGENTIC_SHIFT_ROADMAP.md) — Deep dive on the workflow

## License

MIT
