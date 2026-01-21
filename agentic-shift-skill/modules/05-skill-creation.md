# Module 5: Skill Creation - Capturing Team Patterns

## Learning Objectives

By the end of this module, learners will:
- Identify repetitive workflows suitable for skills
- Create a complete skill with manifest and instructions
- Install and test their custom skill
- Understand how to share skills with their team

## What Are Skills?

### Teaching Points

Skills are **reusable workflows** packaged for Claude Code. Think of them as:

- **Saved prompts** with structure and context
- **Team playbooks** encoded for AI execution
- **Automation recipes** triggered by a command

### Anatomy of a Skill

```
my-skill/
├── skill.json       # Metadata: name, commands, description
├── instructions.md  # What Claude should do when invoked
└── templates/       # Optional: starter files, configs
```

## Lesson 5.1: Identifying Skill Opportunities

### The Skill Identification Framework

Ask yourself:

| Question | If Yes → Skill Candidate |
|----------|-------------------------|
| Do I prompt Claude for this weekly? | High |
| Does it involve multiple steps? | High |
| Do new team members ask how to do it? | High |
| Should everyone do it the same way? | High |
| Is there a specific output format? | Medium |
| Does it require external context? | Medium |

### Common Skill Ideas

| Skill | Trigger | Purpose |
|-------|---------|---------|
| `/standup` | Daily | Generate standup from git/jira activity |
| `/release-notes` | Pre-release | Compile changelog from merged PRs |
| `/review-pr` | Code review | Comprehensive PR analysis |
| `/incident-report` | Post-outage | Structured post-mortem template |
| `/new-endpoint` | Feature work | Scaffold API endpoint with tests |
| `/refactor-suggest` | Tech debt | Analyze file for improvements |
| `/explain-codebase` | Onboarding | Generate architecture overview |

### Discovery Exercise

Ask the learner:

1. "What do you find yourself asking Claude repeatedly?"
2. "What tasks do you wish you could automate?"
3. "What workflow would help a new team member the most?"

Based on their answer, guide them to create that skill.

## Lesson 5.2: Creating skill.json

### Manifest Structure

```json
{
  "name": "skill-name",
  "version": "1.0.0",
  "description": "One-line description of what this skill does",
  "author": "Your Name or Team",
  "commands": [
    {
      "name": "command-name",
      "description": "What this command does"
    }
  ],
  "entry": "instructions.md"
}
```

### Field Details

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Unique identifier, lowercase with hyphens |
| `version` | Yes | Semantic version (1.0.0) |
| `description` | Yes | Brief explanation for skill discovery |
| `author` | No | Credit for the skill creator |
| `commands` | Yes | Array of available commands |
| `entry` | Yes | Path to main instructions file |

### Commands Array

Each command creates an invokable `/command`:

```json
"commands": [
  {
    "name": "review-pr",
    "description": "Perform comprehensive PR review"
  },
  {
    "name": "review-pr-quick",
    "description": "Quick PR review focusing on critical issues only"
  }
]
```

## Lesson 5.3: Writing instructions.md

### Structure Template

```markdown
# Skill Name

Brief description of what this skill does and when to use it.

## Commands

### /command-name

Description of this specific command.

## Input

What the user provides when invoking:
- Arguments (e.g., PR number, file path)
- Context (e.g., current directory, git state)

## Process

Step-by-step instructions for Claude:

### Step 1: Gather Context
[What to read, query, or check]

### Step 2: Analyze
[What to evaluate or process]

### Step 3: Generate Output
[What to produce and in what format]

## Output Format

Exact format for the response:

```
## Title
[content]

### Section
[content]
```

## Examples

### Example 1: [Scenario]

**Input**: [what user provided]

**Output**: [expected response]

## Edge Cases

- If [condition], then [action]
- If [error], then [fallback]
```

## Interactive Exercise 5.1: Create Your First Skill

### Option A: PR Review Skill

Create a comprehensive PR review skill:

**skill.json:**
```json
{
  "name": "pr-review",
  "version": "1.0.0",
  "description": "Comprehensive pull request review with security and style analysis",
  "author": "Your Team",
  "commands": [
    {
      "name": "review-pr",
      "description": "Review a pull request thoroughly"
    }
  ],
  "entry": "instructions.md"
}
```

**instructions.md:**
```markdown
# PR Review Skill

Perform a comprehensive code review of a pull request.

## Input

The user provides:
- PR number (e.g., "123") or URL
- Optional: Focus area ("security", "performance", "all")

## Process

### Step 1: Fetch PR Information

Use GitHub MCP (if available) or ask user to provide:
- PR title and description
- List of changed files
- Diff content
- Existing review comments

### Step 2: Analyze Changes

For each changed file, evaluate:

**Correctness**
- Does the code match the PR description?
- Are edge cases handled?
- Is error handling appropriate?

**Security**
- Input validation present?
- No hardcoded secrets?
- Safe data handling?

**Performance**
- Efficient algorithms?
- No N+1 queries?
- Appropriate caching?

**Maintainability**
- Clear naming?
- Reasonable function length?
- DRY principles?

### Step 3: Generate Review

## Output Format

```markdown
## PR Review: #[number] - [title]

### Summary
[2-3 sentence overview of the changes]

### Verdict: [APPROVE | REQUEST_CHANGES | COMMENT]

### Findings

#### Critical Issues
- [list or "None found"]

#### Suggestions
- [list of improvements]

#### Positive Notes
- [good patterns observed]

### Files Reviewed
| File | Status | Notes |
|------|--------|-------|
| path/to/file | OK/WARN | brief note |
```

## Edge Cases

- If PR is too large (>50 files), suggest breaking into smaller PRs
- If tests are missing, note as critical issue
- If no GitHub MCP, ask user to paste the diff
```

### Option B: Standup Generator Skill

**skill.json:**
```json
{
  "name": "standup",
  "version": "1.0.0",
  "description": "Generate daily standup from git activity and issues",
  "commands": [
    {
      "name": "standup",
      "description": "Generate standup summary for today"
    }
  ],
  "entry": "instructions.md"
}
```

**instructions.md:**
```markdown
# Standup Generator Skill

Generate a daily standup summary based on git activity.

## Process

### Step 1: Gather Git Activity

Run these commands to collect data:
- `git log --oneline --since="yesterday" --author="$(git config user.email)"`
- `git branch --show-current`
- `git status --short`

### Step 2: Check for Open Work

If GitHub MCP available:
- List PRs authored by user
- List PRs where user is requested reviewer
- List assigned issues

### Step 3: Generate Standup

## Output Format

```markdown
## Standup - [Today's Date]

### Completed Yesterday
- [commit/PR summary]
- [commit/PR summary]

### Working On Today
- [current branch/task]
- [pending reviews]

### Blockers
- [list or "None"]

### Notes
- [any relevant context]
```

## Edge Cases

- If no commits yesterday, check last 3 days
- If not in git repo, skip git activity
- If no issues/PRs, omit those sections
```

## Lesson 5.4: Installing Your Skill

### Local Installation

```bash
# Create skills directory if it doesn't exist
mkdir -p ~/.claude/skills

# Copy your skill
cp -r ./my-skill ~/.claude/skills/

# Or for development, symlink it
ln -s $(pwd)/my-skill ~/.claude/skills/my-skill
```

### Project-Local Skills

For skills specific to one project:

```bash
# Create project skills directory
mkdir -p .claude/skills

# Add skill there
cp -r ./my-skill .claude/skills/
```

Project-local skills take precedence over global ones.

### Verification

```bash
# List available skills
claude /help

# Your skill should appear in the list
# Test it
claude /your-command
```

## Lesson 5.5: Sharing Skills

### Option A: Git Repository

```bash
# Create a dedicated skills repo
git init team-claude-skills
cd team-claude-skills

# Add skills
cp -r /path/to/skill1 ./
cp -r /path/to/skill2 ./

# Commit and push
git add .
git commit -m "Add team skills"
git push origin main
```

Team members clone and symlink:
```bash
git clone git@github.com:team/claude-skills.git ~/claude-skills
ln -s ~/claude-skills/* ~/.claude/skills/
```

### Option B: Zip Distribution

```bash
# Package skill
cd my-skill
zip -r ../my-skill.zip .

# Share via Slack, email, etc.
# Recipients unzip to ~/.claude/skills/
```

### Option C: npm Package (Advanced)

For public distribution:

```json
{
  "name": "@yourorg/claude-skill-name",
  "version": "1.0.0",
  "files": ["skill.json", "instructions.md", "templates/"]
}
```

## Lesson 5.6: Skill Best Practices

### Do

- **Test before sharing**: Run through all commands
- **Document edge cases**: What happens with bad input?
- **Version appropriately**: Bump version when changing behavior
- **Keep focused**: One skill = one workflow

### Don't

- **Over-scope**: Don't try to do everything in one skill
- **Hard-code paths**: Use relative paths and environment variables
- **Assume context**: Check for required tools/MCPs
- **Skip examples**: They help users understand expected behavior

## Checkpoint

Module 5 is complete when:
- [ ] User identified a repetitive workflow for their skill
- [ ] Created valid skill.json manifest
- [ ] Wrote comprehensive instructions.md
- [ ] Installed skill to ~/.claude/skills/ or .claude/skills/
- [ ] Successfully invoked skill via /command
- [ ] Understands how to share skill with team

## Graduation

---

# Congratulations! You've Completed The Agentic Shift

You've transformed from a manual coder into an **Architect and Reviewer**.

## What You've Mastered

| Module | Skill |
|--------|-------|
| Foundation | Creating effective project context with CLAUDE.md |
| MCP Mastery | Connecting Claude to external tools and data |
| TDA Cycle | Test-driven agentic development workflow |
| Governance | Automated quality gates and security scanning |
| Skill Creation | Capturing patterns as reusable skills |

## Your New Daily Workflow

```
Morning:    /standup → Plan your day with context
Coding:     Write tests → Prompt Claude → Iterate
Review:     /review-pr → Comprehensive analysis
Commit:     Pre-commit hooks → Catch issues early
Push:       Pre-push audit → Security verification
Weekly:     Identify patterns → Create new skills
```

## Quick Reference Card

```
Commands:
  claude /init           Initialize Claude in a project
  claude /help           List available commands and skills
  claude /skill-name     Run a custom skill

Files:
  CLAUDE.md              Project context for Claude
  .claude/settings.json  Project configuration
  ~/.claude/mcp_servers.json  MCP server config
  ~/.claude/skills/      Global skills directory
  .claude/skills/        Project-local skills

TDA Cycle:
  1. Write failing tests
  2. Prompt: "Make these tests pass"
  3. Run tests
  4. Iterate if needed
```

## Next Steps

1. **Create 2-3 more skills** for your common workflows
2. **Share this onboarding** with your team
3. **Customize hooks** for your project's specific requirements
4. **Build a team skills library** in a shared repository

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/claude-code)
- [MCP Server Registry](https://github.com/modelcontextprotocol/servers)
- [The Agentic Shift Roadmap](./THE_AGENTIC_SHIFT_ROADMAP.md)

---

Welcome to the future of software development. You are now an **Architect and Reviewer**.

---
