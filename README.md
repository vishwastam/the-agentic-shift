# The Agentic Shift

Transform your team from manual coders into **Architects and Reviewers** with Claude Code.

## The Core Workflow

```
Specify → Context → Plan → Generate Tests → Generate Code → Review → Validate
  [You]   [Claude]  [Interactive]  [Claude]    [Claude]      [AI]    [Claude+You]
```

**Your job**: Define WHAT should exist (PRD, spec, user story), approve at checkpoints
**Claude's job**: Figure out HOW, run AI review, iterate on test failures until passing

## What's Included

### 1. Strategic Roadmap

[`THE_AGENTIC_SHIFT_ROADMAP.md`](./THE_AGENTIC_SHIFT_ROADMAP.md)

A self-paced guide covering:
- Environment setup and security
- The Specification-Driven workflow
- MCP integration (optional)
- Governance with git hooks (optional)

### 2. Interactive Onboarding Skill

[`agentic-shift-skill/`](./agentic-shift-skill/)

Hands-on training with 5 modules:

| Module | Topic | Required? |
|--------|-------|-----------|
| Foundation | CLAUDE.md and context | Yes |
| Spec-Driven Dev | Write specs, review generated code | Yes (core) |
| MCP | External tool integration | Optional |
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

## Example: The New Way to Build Features

**1. You write a spec:**
```markdown
## Feature: User Registration

### Requirements
- POST /api/users endpoint
- Accept email and password
- Validate email format, password min 8 chars
- Return 400 for validation errors
- Return 409 if email exists
```

**2. Give spec to Claude:**
```
Here's my feature spec: [paste spec]

Ask me clarifying questions about my codebase, then:
1. Create an execution plan for my approval
2. Propose tests based on requirements
3. Wait for my approval
4. Implement code, run AI review
5. Run tests and iterate until all pass
```

**3. Answer Claude's questions:**
```
"We use Prisma + PostgreSQL. Put it in src/routes/users.ts.
Use bcrypt for passwords. Errors are { error: string, code: number }."
```

**4. Review execution plan, approve or adjust.**

**5. Review generated tests, approve.**

**6. Claude runs AI review, runs tests, fixes failures, presents final diff for your approval.**

## Templates

- `SPEC.md.template` - Feature specification template
- `CLAUDE.md.template` - Project context template
- `pre-commit.template` - Security hook

## Requirements

- Claude Code CLI installed and authenticated
- A project to work with

## License

MIT
