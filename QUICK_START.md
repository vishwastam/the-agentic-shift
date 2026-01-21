# Quick Start (5 Minutes)

Get your first AI-assisted feature shipped.

## Prerequisites

- Claude Code CLI installed (`npm install -g @anthropic-ai/claude-code`)
- Authenticated (`claude login`)
- A project to work in

## Step 1: Initialize (30 seconds)

```bash
cd your-project
claude /init
```

This creates `CLAUDE.md`. Open it and add one line:

```markdown
Tech stack: [your stack, e.g., "Node.js, Express, PostgreSQL, Jest"]
```

That's it. You can add more context later.

## Step 2: Write a Spec (2 minutes)

Create a simple spec for something you need. Example:

```markdown
## Feature: Health Check Endpoint

### Requirements
- GET /health returns { status: "ok" }
- Returns 200 when server is running
```

## Step 3: Run the Workflow (2 minutes)

```bash
claude
```

Then paste:

```
Here's my feature spec:

[paste your spec]

Ask me clarifying questions, then:
1. Propose tests
2. Implement code to pass the tests
```

Claude will:
1. Ask questions about your codebase
2. Generate tests
3. Implement code
4. Run tests and fix failures

You review and approve the final diff.

## Done

You've completed your first AI-assisted feature.

## What's Next?

| If you want to... | Read |
|-------------------|------|
| Understand the full workflow | [Module 3: Core Workflow](agentic-shift-skill/modules/03-tda-cycle.md) |
| Connect GitHub, Figma, databases | [Module 2: MCP Add-ons](agentic-shift-skill/modules/02-mcp-mastery.md) |
| Add security hooks | [Module 4: Governance](agentic-shift-skill/modules/04-governance.md) |
| Know when NOT to use this | [Limitations](LIMITATIONS.md) |
| Enterprise/compliance needs | [Enterprise Guide](ENTERPRISE.md) |

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Claude generates wrong patterns | Add more context to CLAUDE.md |
| Tests keep failing | Say "show me the error" and debug together |
| Too expensive | Set token budgets in Anthropic Console |
| Doesn't know my framework | Specify it in your spec or CLAUDE.md |
