# The Agentic Shift: Strategic Roadmap

> A framework for transitioning software teams from manual coding to agent-led development with Claude Code.

---

## Executive Summary

The Agentic Shift transforms developers from manual coders into **Architects and Reviewers**—professionals who define intent, establish constraints, and validate outcomes while Claude Code handles implementation details.

### The New Developer Role

| Traditional Role | Agentic Role |
|-----------------|--------------|
| Write every line of code | Define specifications and constraints |
| Debug through trial and error | Write tests that describe desired behavior |
| Context-switch between tools | Orchestrate AI with integrated tooling |
| Manual code review | Validate AI-generated solutions |
| Repeat patterns manually | Capture patterns as reusable skills |

---

## Phase 1: Foundation (Week 1-2)

### 1.1 Environment Setup

#### Individual Developer Setup

```bash
# Install Claude Code CLI
npm install -g @anthropic-ai/claude-code

# Authenticate
claude login

# Verify installation
claude --version
```

#### Team Configuration Checklist

| Component | Action | Owner |
|-----------|--------|-------|
| API Access | Provision team API keys via Anthropic Console | DevOps |
| SSO Integration | Configure SAML/OIDC if using enterprise auth | Security |
| Usage Limits | Set per-developer token budgets | Engineering Lead |
| Audit Logging | Enable API request logging | Compliance |

#### Repository Initialization

```bash
# In each project repository
claude /init

# This creates:
# - .claude/settings.json (project config)
# - CLAUDE.md (context document)
```

### 1.2 Security Guardrails

#### Tier 1: Mandatory Controls

Create `.claude/settings.json` in each repository:

```json
{
  "permissions": {
    "allowedTools": ["Read", "Edit", "Write", "Glob", "Grep", "Bash"],
    "blockedCommands": [
      "rm -rf /",
      "rm -rf ~",
      "DROP DATABASE",
      "DROP TABLE",
      "curl * | bash",
      "wget * | sh",
      "chmod 777",
      "> /dev/sda"
    ],
    "requireConfirmation": [
      "git push",
      "git push --force",
      "npm publish",
      "docker push",
      "terraform apply",
      "kubectl delete"
    ]
  },
  "secrets": {
    "scanEnabled": true,
    "blockOnDetection": true,
    "patterns": [
      "API_KEY",
      "SECRET",
      "PASSWORD",
      "TOKEN",
      "PRIVATE_KEY",
      "AWS_ACCESS",
      "GITHUB_TOKEN"
    ]
  }
}
```

#### Tier 2: Code Review Gates

Add to `.git/hooks/pre-commit`:

```bash
#!/bin/bash
set -e

STAGED_FILES=$(git diff --cached --name-only --diff-filter=ACM)

if [ -z "$STAGED_FILES" ]; then
  exit 0
fi

echo "Running Claude security audit..."

RESULT=$(claude --print "Review these staged files for security issues:
$STAGED_FILES

Check for: SQL injection, XSS, hardcoded secrets, insecure dependencies.
Output ONLY: PASS or FAIL followed by a brief reason.")

echo "$RESULT"

if echo "$RESULT" | grep -qi "^FAIL"; then
  echo "Security audit failed. Please fix issues before committing."
  exit 1
fi
```

#### Tier 3: Network & Data Controls

| Control | Implementation |
|---------|----------------|
| Egress Filtering | Restrict Claude's web access to approved domains |
| PII Scanning | Prevent sensitive data in prompts via preprocessing |
| Audit Trail | Log all Claude interactions with timestamps |
| Data Residency | Configure region-specific API endpoints if required |

---

## Phase 2: Tooling Integration (Week 3-4)

### 2.1 Model Context Protocol (MCP) Selection

MCPs extend Claude Code with real-time access to external systems. Select based on your team's needs:

| MCP Server | Use Case | Priority | Setup Complexity |
|------------|----------|----------|------------------|
| **GitHub** | PR reviews, issue triage, code search | High | Low |
| **Filesystem** | Enhanced file operations | High | Low |
| **Postgres** | Schema exploration, query generation | High | Medium |
| **Slack** | Team notifications, standup automation | Medium | Low |
| **Jira** | Sprint planning, ticket management | Medium | Medium |
| **Playwright** | E2E test generation, browser automation | Medium | High |
| **Memory** | Persistent context across sessions | Low | Low |

### 2.2 MCP Configuration

Create or update `~/.claude/mcp_servers.json`:

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${GITHUB_TOKEN}"
      }
    },
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "${DATABASE_URL}"
      }
    },
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "${SLACK_BOT_TOKEN}"
      }
    },
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/directory"]
    }
  }
}
```

### 2.3 Validation Checklist

After configuration, verify each MCP:

```bash
# GitHub: List recent issues
claude "List the 5 most recent open issues in this repository"

# Postgres: Describe schema
claude "Show me all tables and their columns in the database"

# Slack: Test notification
claude "Send a test message to #engineering-test channel"
```

---

## Phase 3: Workflow Transformation (Week 5-8)

### 3.1 The Architect-Reviewer Model

```
┌─────────────────────────────────────────────────────────────────┐
│                    TRADITIONAL WORKFLOW                          │
│  Developer → Code → Test → Debug → Code → Test → PR → Review    │
│                    [High cognitive load]                         │
└─────────────────────────────────────────────────────────────────┘
                              ↓
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

### 3.2 The Test-Driven Agentic (TDA) Cycle

This is the core workflow pattern:

```
    ┌─────────────────────────────────────────┐
    │                                         │
    │   1. WRITE        2. PROMPT             │
    │      TEST    →       CLAUDE             │
    │   (Human)         (Agent)               │
    │                                         │
    │        ↑              ↓                 │
    │                                         │
    │   4. REFINE  ←    3. RUN                │
    │      PROMPT        TESTS                │
    │   (If failing)    (Automated)           │
    │                                         │
    └─────────────────────────────────────────┘
```

**Key Principle**: Tests are your specification language. Write what you want, let Claude figure out how.

#### Example TDA Session

```bash
# Step 1: Write a failing test
cat > tests/auth.test.ts << 'EOF'
import { validateToken, TokenError } from '../src/auth';

describe('validateToken', () => {
  it('returns user data for valid JWT', async () => {
    const token = createTestJWT({ userId: '123', role: 'admin' });
    const result = await validateToken(token);
    expect(result.userId).toBe('123');
    expect(result.role).toBe('admin');
  });

  it('throws TokenError for expired tokens', async () => {
    const expired = createTestJWT({ exp: Date.now() / 1000 - 3600 });
    await expect(validateToken(expired)).rejects.toThrow(TokenError);
  });
});
EOF

# Step 2: Run test (confirms it fails)
npm test -- tests/auth.test.ts

# Step 3: Prompt Claude
claude "Make the tests in tests/auth.test.ts pass.
        Create src/auth.ts with validateToken function.
        Use jose library for JWT validation.
        Follow existing code patterns in src/"

# Step 4: Run tests again, iterate if needed
npm test -- tests/auth.test.ts
```

### 3.3 Daily Workflow Integration

#### Morning Standup

```bash
claude "Analyze my git commits from yesterday and any Jira tickets
        I'm assigned to. Generate a standup summary with:
        - What I completed
        - What I'm working on today
        - Any blockers"
```

#### Feature Development

1. **Understand**: "Explain how the current authentication flow works"
2. **Plan**: "What files need to change to add OAuth support?"
3. **Specify**: Write tests describing the new behavior
4. **Implement**: "Make these tests pass following our patterns"
5. **Validate**: Review diff, run full test suite
6. **Ship**: Approve and merge

#### Code Review

```bash
claude "Review PR #123. Check for:
        - Logic errors
        - Security issues
        - Performance concerns
        - Style consistency
        Provide specific, actionable feedback."
```

---

## Phase 4: Governance & Compliance (Week 9-12)

### 4.1 Pre-Push Audit Pipeline

Create `.git/hooks/pre-push`:

```bash
#!/bin/bash
set -e

echo "═══════════════════════════════════════"
echo "  Claude Code Pre-Push Audit"
echo "═══════════════════════════════════════"

BRANCH=$(git rev-parse --abbrev-ref HEAD)
CHANGED=$(git diff --name-only origin/main...HEAD 2>/dev/null || git diff --name-only HEAD~5...HEAD)

if [ -z "$CHANGED" ]; then
  echo "No changes to audit."
  exit 0
fi

echo "Branch: $BRANCH"
echo "Files changed:"
echo "$CHANGED"
echo ""

# Security Analysis
echo "→ Security Analysis..."
SECURITY=$(claude --print "Analyze these files for OWASP Top 10 vulnerabilities:

$CHANGED

Check for:
1. Injection flaws (SQL, command, LDAP)
2. Broken authentication
3. Sensitive data exposure
4. XXE vulnerabilities
5. Broken access control
6. Security misconfiguration
7. XSS vulnerabilities
8. Insecure deserialization
9. Using components with known vulnerabilities
10. Insufficient logging

Output format:
SEVERITY: [CRITICAL|HIGH|MEDIUM|LOW|NONE]
ISSUES: [list each issue, or 'None found']
RECOMMENDATION: [action items]")

echo "$SECURITY"
echo ""

if echo "$SECURITY" | grep -q "SEVERITY: CRITICAL"; then
  echo "╔═══════════════════════════════════════╗"
  echo "║  BLOCKED: Critical security issues    ║"
  echo "╚═══════════════════════════════════════╝"
  exit 1
fi

if echo "$SECURITY" | grep -q "SEVERITY: HIGH"; then
  echo "⚠️  High severity issues found. Review before pushing."
  read -p "Continue anyway? (y/N) " -n 1 -r
  echo
  if [[ ! $REPLY =~ ^[Yy]$ ]]; then
    exit 1
  fi
fi

echo "✓ Security audit passed"
echo ""

# Style Compliance
echo "→ Style Compliance Check..."
claude --print "Review code style for: $CHANGED
Report only violations of common best practices.
Be concise - list file:line and issue only."

echo ""
echo "═══════════════════════════════════════"
echo "  Audit Complete - Proceeding with push"
echo "═══════════════════════════════════════"
```

Make executable:
```bash
chmod +x .git/hooks/pre-push
```

### 4.2 Metrics & KPIs

Track these metrics to measure the transition's success:

| Metric | How to Measure | Baseline | Target |
|--------|---------------|----------|--------|
| **Time to First PR** | Git timestamps from branch creation to PR | 4+ hours | < 1 hour |
| **PR Review Cycles** | Average rounds of feedback per PR | 3+ | < 2 |
| **Bug Escape Rate** | Bugs found post-deploy / total deploys | > 10% | < 5% |
| **Test Coverage Delta** | Coverage change per sprint | ±0% | +2% |
| **Developer Satisfaction** | Quarterly NPS survey | Baseline | +20 points |
| **Security Findings** | Critical/High issues in pre-push audit | N/A | 0 |

### 4.3 Compliance Checklist

Before declaring the transition complete:

- [ ] All repositories have CLAUDE.md files
- [ ] Pre-commit/pre-push hooks installed organization-wide
- [ ] MCP servers configured for critical integrations
- [ ] Team completed onboarding skill (all 5 modules)
- [ ] At least 3 team-specific skills created
- [ ] Metrics dashboard operational
- [ ] Security review of Claude Code permissions complete
- [ ] Incident response plan updated for AI-assisted development

---

## Quick Reference

### Essential Commands

```bash
# Initialize project
claude /init

# Run with specific context
claude --context "Focus on security"

# Print output without interaction
claude --print "Summarize recent changes"

# Use a skill
claude /skill-name

# List available skills
claude /help
```

### Effective Prompting Patterns

| Pattern | Example |
|---------|---------|
| **Constrained** | "Modify ONLY the handleAuth function in src/auth.ts" |
| **Test-First** | "Make tests/auth.test.ts pass without changing the tests" |
| **Pattern-Following** | "Follow the existing error handling pattern in src/errors.ts" |
| **Incremental** | "First, explain your plan. Then implement step by step." |
| **Review-Oriented** | "Show me the diff before applying any changes" |

### Red Flags to Watch For

- Claude suggesting changes outside the requested scope
- Generated code that doesn't match project patterns
- Security-sensitive operations without confirmation
- Modifications to configuration or infrastructure files
- Any action that feels "too automatic"

---

## Appendix: Rollout Timeline

| Week | Phase | Milestone | Success Criteria |
|------|-------|-----------|------------------|
| 1 | Foundation | CLI installed | 100% of team authenticated |
| 2 | Foundation | CLAUDE.md created | Every active repo has context |
| 3 | Integration | First MCP | GitHub MCP working |
| 4 | Integration | Full MCP suite | All critical integrations live |
| 5 | Adoption | TDA training | Team completed Module 3 |
| 6 | Adoption | First TDA feature | One feature shipped via TDA |
| 7 | Governance | Hooks installed | Pre-push on all repos |
| 8 | Governance | Metrics live | Dashboard tracking KPIs |
| 9 | Optimization | First skill | Team-specific skill deployed |
| 10 | Optimization | Skill library | 3+ skills in rotation |
| 11 | Review | Retrospective | Feedback collected |
| 12 | Complete | Full adoption | All targets met |

---

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/claude-code)
- [MCP Server Registry](https://github.com/modelcontextprotocol/servers)
- [Onboarding Skill](/agentic-shift-onboard) - Interactive tutorial

---

*The Agentic Shift v1.0 - Transform how your team builds software.*
