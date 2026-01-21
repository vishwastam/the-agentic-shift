# The Agentic Shift - Onboarding Skill

An interactive onboarding tutorial for Claude Code that transforms developers from manual coders into **Architects and Reviewers**.

## What You'll Learn

| Module | Topic | Duration |
|--------|-------|----------|
| 1 | **Foundation** - CLAUDE.md and project context | ~15 min |
| 2 | **MCP Mastery** - External tool integration | ~20 min |
| 3 | **TDA Cycle** - Test-Driven Agentic development | ~25 min |
| 4 | **Governance** - Pre-commit hooks and quality gates | ~15 min |
| 5 | **Skill Creation** - Building reusable workflows | ~15 min |

**Total: ~90 minutes (self-paced)**

## Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/claude-code) installed and authenticated
- A project directory with source code (or willingness to create a practice project)
- Git initialized in the project

```bash
# Verify Claude Code is installed
claude --version

# Verify you're authenticated
claude "Hello"
```

## Installation

### Option 1: Quick Install (Recommended)

```bash
# Clone to your skills directory
git clone https://github.com/anthropics/agentic-shift-onboard.git ~/.claude/skills/agentic-shift-onboard

# Or if you downloaded as a zip
unzip agentic-shift-onboard.zip -d ~/.claude/skills/
```

### Option 2: Manual Install

```bash
# Create skills directory if it doesn't exist
mkdir -p ~/.claude/skills

# Copy the skill folder
cp -r /path/to/agentic-shift-skill ~/.claude/skills/agentic-shift-onboard
```

### Option 3: Development/Symlink

```bash
# Symlink for active development
ln -s /path/to/agentic-shift-skill ~/.claude/skills/agentic-shift-onboard
```

## Usage

### Start Full Onboarding

```bash
claude /onboard
```

This starts the complete interactive tutorial from Module 1.

### Jump to Specific Modules

```bash
# Module 1: Foundation (CLAUDE.md)
claude /onboard-foundation

# Module 2: MCP Integration
claude /onboard-mcp

# Module 3: Test-Driven Agentic
claude /onboard-tda

# Module 4: Governance Hooks
claude /onboard-governance

# Module 5: Skill Creation
claude /onboard-skills
```

## Directory Structure

```
agentic-shift-skill/
├── skill.json              # Skill manifest
├── instructions.md         # Main entry point and orchestration
├── modules/
│   ├── 01-foundation.md    # CLAUDE.md creation
│   ├── 02-mcp-mastery.md   # MCP integration
│   ├── 03-tda-cycle.md     # Test-driven agentic
│   ├── 04-governance.md    # Pre-commit/pre-push hooks
│   └── 05-skill-creation.md # Creating custom skills
├── templates/
│   ├── CLAUDE.md.template  # Starter template for CLAUDE.md
│   ├── pre-commit.template # Pre-commit hook template
│   └── pre-push.template   # Pre-push hook template
└── README.md               # This file
```

## Templates

The skill includes ready-to-use templates:

### CLAUDE.md Template

```bash
# Copy to your project and fill in the blanks
cp ~/.claude/skills/agentic-shift-onboard/templates/CLAUDE.md.template ./CLAUDE.md
```

### Git Hooks

```bash
# Install pre-commit hook
cp ~/.claude/skills/agentic-shift-onboard/templates/pre-commit.template .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit

# Install pre-push hook
cp ~/.claude/skills/agentic-shift-onboard/templates/pre-push.template .git/hooks/pre-push
chmod +x .git/hooks/pre-push
```

## Companion Document

This skill is part of **The Agentic Shift** framework. For the complete strategic roadmap including team rollout plans, metrics, and governance policies, see:

[THE_AGENTIC_SHIFT_ROADMAP.md](../THE_AGENTIC_SHIFT_ROADMAP.md)

## Customization

### For Your Team

1. Fork this repository
2. Modify `instructions.md` to include team-specific patterns
3. Update module files with your conventions
4. Add custom templates for your tech stack
5. Distribute to team via git or zip

### Adding Modules

Create a new file in `modules/` and register it in `skill.json`:

```json
{
  "commands": [
    // ... existing commands
    {
      "name": "onboard-custom",
      "description": "Your custom module"
    }
  ],
  "modules": {
    // ... existing modules
    "custom": "modules/06-custom.md"
  }
}
```

## Troubleshooting

### Skill not appearing in `/help`

1. Verify the skill is in the correct location:
   ```bash
   ls ~/.claude/skills/agentic-shift-onboard/skill.json
   ```

2. Check `skill.json` is valid JSON:
   ```bash
   cat ~/.claude/skills/agentic-shift-onboard/skill.json | python -m json.tool
   ```

3. Restart Claude Code

### MCP servers not connecting

1. Verify environment variables are set:
   ```bash
   echo $GITHUB_TOKEN
   ```

2. Check MCP config syntax:
   ```bash
   cat ~/.claude/mcp_servers.json | python -m json.tool
   ```

3. Test the MCP server directly:
   ```bash
   npx -y @modelcontextprotocol/server-github --help
   ```

### Hooks not running

1. Verify hooks are executable:
   ```bash
   ls -la .git/hooks/pre-commit
   # Should show -rwxr-xr-x
   ```

2. Make executable if needed:
   ```bash
   chmod +x .git/hooks/pre-commit
   ```

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Test your changes with the onboarding flow
4. Submit a pull request

## License

MIT License - Feel free to use, modify, and distribute.

## Credits

Created as part of **The Agentic Shift** framework for transitioning software teams to agent-led development with Claude Code.

---

**Ready to transform how you build software?**

```bash
claude /onboard
```
