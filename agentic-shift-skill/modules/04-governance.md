# Module 4: Governance - Automated Quality Gates

## Learning Objectives

By the end of this module, learners will:
- Install Claude-powered pre-commit hooks for security scanning
- Configure pre-push hooks for comprehensive code audit
- Test hooks with intentional violations to verify they work
- Understand how to deploy hooks team-wide

## Why Automated Governance?

### Teaching Points

Explain:

1. **Humans miss things**: Especially under deadline pressure
2. **Consistency**: Same rules applied to every commit
3. **Shift left**: Catch issues before they reach code review
4. **Documentation**: Hooks codify your team's standards

### The Hook Pipeline

```
Developer commits
       ↓
┌─────────────────────┐
│   PRE-COMMIT HOOK   │ ← Fast checks: secrets, syntax, formatting
│   (~5 seconds)      │
└─────────────────────┘
       ↓ (if passed)
┌─────────────────────┐
│   PRE-PUSH HOOK     │ ← Deep checks: security audit, style review
│   (~30 seconds)     │
└─────────────────────┘
       ↓ (if passed)
Code reaches remote
```

## Lesson 4.1: Understanding Git Hooks

### Hook Location

```bash
# Hooks live in .git/hooks/
ls -la .git/hooks/

# Sample hooks are provided but disabled (.sample extension)
# Active hooks have no extension and are executable
```

### Hook Types

| Hook | When It Runs | Use Case |
|------|--------------|----------|
| `pre-commit` | Before commit is created | Fast syntax/secret checks |
| `commit-msg` | After commit message entered | Message format validation |
| `pre-push` | Before push to remote | Full security audit |
| `post-merge` | After merge completes | Dependency updates |

## Interactive Exercise 4.1: Create Pre-Commit Hook

### Step 1: Create the Hook File

Guide them to create `.git/hooks/pre-commit`:

```bash
#!/bin/bash
#
# Claude-powered pre-commit hook
# Performs fast security and quality checks before commit
#
set -e

echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "  Pre-Commit Security Scan"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

# Get staged files (excluding deleted files)
STAGED=$(git diff --cached --name-only --diff-filter=ACM)

if [ -z "$STAGED" ]; then
  echo "No staged files. Skipping checks."
  exit 0
fi

echo "Scanning files:"
echo "$STAGED" | sed 's/^/  /'
echo ""

# Quick secrets scan using grep patterns
echo "→ Checking for hardcoded secrets..."

SECRETS_FOUND=0
while IFS= read -r file; do
  if [ -f "$file" ]; then
    # Check for common secret patterns
    if grep -Hn --color=never \
      -e "api[_-]key\s*[:=]" \
      -e "secret[_-]key\s*[:=]" \
      -e "password\s*[:=]\s*['\"][^'\"]\+" \
      -e "aws_access_key_id" \
      -e "aws_secret_access_key" \
      -e "ghp_[a-zA-Z0-9]\{36\}" \
      -e "sk-[a-zA-Z0-9]\{48\}" \
      "$file" 2>/dev/null; then
      SECRETS_FOUND=1
    fi
  fi
done <<< "$STAGED"

if [ $SECRETS_FOUND -eq 1 ]; then
  echo ""
  echo "╔═══════════════════════════════════════╗"
  echo "║  BLOCKED: Potential secrets detected  ║"
  echo "╚═══════════════════════════════════════╝"
  echo ""
  echo "Remove secrets and use environment variables instead."
  echo "If these are false positives, you can bypass with:"
  echo "  git commit --no-verify"
  exit 1
fi

echo "  ✓ No secrets detected"
echo ""

# Claude-powered quick analysis (optional, can be slow)
if command -v claude &> /dev/null; then
  echo "→ Running Claude quick analysis..."

  ANALYSIS=$(claude --print "Quick security scan of these staged files.
Check for:
1. Obvious SQL injection patterns
2. Hardcoded credentials (that grep might miss)
3. Dangerous function calls (eval, exec, etc.)

Files: $STAGED

Respond in exactly this format:
STATUS: [PASS|WARN|FAIL]
ISSUES: [one line summary or 'None']" 2>/dev/null || echo "STATUS: PASS
ISSUES: Claude unavailable, skipped")

  echo "$ANALYSIS"

  if echo "$ANALYSIS" | grep -q "STATUS: FAIL"; then
    echo ""
    echo "Security issues found. Please review and fix."
    exit 1
  fi
fi

echo ""
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
echo "  ✓ Pre-commit checks passed"
echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"
```

### Step 2: Make Executable

```bash
chmod +x .git/hooks/pre-commit
```

### Step 3: Test the Hook

Create a test file with an intentional secret:

```bash
# Create a file with a fake secret
echo 'const API_KEY = "sk-1234567890abcdef1234567890abcdef12345678901234567890";' > test-secret.js

# Try to commit
git add test-secret.js
git commit -m "test secret detection"
```

Expected: Commit should be blocked with a warning about the detected secret.

### Step 4: Clean Up

```bash
git reset HEAD test-secret.js
rm test-secret.js
```

## Interactive Exercise 4.2: Create Pre-Push Hook

### Step 1: Create the Hook File

Guide them to create `.git/hooks/pre-push`:

```bash
#!/bin/bash
#
# Claude-powered pre-push hook
# Performs comprehensive security and style audit before push
#
set -e

echo "═══════════════════════════════════════"
echo "  Claude Code Pre-Push Audit"
echo "═══════════════════════════════════════"

# Get the remote and branch being pushed to
REMOTE="$1"
URL="$2"

echo "Remote: $REMOTE ($URL)"

# Get commits that will be pushed
# This reads from stdin in the format: <local ref> <local sha> <remote ref> <remote sha>
while read local_ref local_sha remote_ref remote_sha; do
  if [ "$local_sha" = "0000000000000000000000000000000000000000" ]; then
    # Branch being deleted, skip
    continue
  fi

  if [ "$remote_sha" = "0000000000000000000000000000000000000000" ]; then
    # New branch, compare with default branch
    RANGE="origin/main...$local_sha"
  else
    # Existing branch, compare with remote
    RANGE="$remote_sha...$local_sha"
  fi

  # Get changed files
  CHANGED=$(git diff --name-only $RANGE 2>/dev/null || git diff --name-only HEAD~10...HEAD)

  if [ -z "$CHANGED" ]; then
    echo "No files changed. Skipping audit."
    continue
  fi

  echo ""
  echo "Files to audit:"
  echo "$CHANGED" | sed 's/^/  /'
  echo ""

  # Check if Claude is available
  if ! command -v claude &> /dev/null; then
    echo "⚠ Claude CLI not found. Skipping deep analysis."
    echo "  Install with: npm install -g @anthropic-ai/claude-code"
    exit 0
  fi

  # Security Analysis
  echo "→ Running security analysis..."
  SECURITY=$(claude --print "Analyze these changed files for security vulnerabilities:

$CHANGED

Check for OWASP Top 10:
1. Injection (SQL, command, LDAP)
2. Broken authentication
3. Sensitive data exposure
4. XML external entities (XXE)
5. Broken access control
6. Security misconfiguration
7. Cross-site scripting (XSS)
8. Insecure deserialization
9. Known vulnerable components
10. Insufficient logging

Respond in this exact format:
SEVERITY: [CRITICAL|HIGH|MEDIUM|LOW|NONE]
ISSUES:
- [issue 1 or 'None found']
- [issue 2]
RECOMMENDATION: [action or 'Proceed with push']")

  echo "$SECURITY"
  echo ""

  # Block on critical issues
  if echo "$SECURITY" | grep -q "SEVERITY: CRITICAL"; then
    echo "╔═══════════════════════════════════════╗"
    echo "║  BLOCKED: Critical security issues    ║"
    echo "╚═══════════════════════════════════════╝"
    echo ""
    echo "Fix the critical issues above before pushing."
    exit 1
  fi

  # Warn on high issues
  if echo "$SECURITY" | grep -q "SEVERITY: HIGH"; then
    echo "⚠ High severity issues found."
    read -p "Continue with push anyway? (y/N) " -n 1 -r </dev/tty
    echo
    if [[ ! $REPLY =~ ^[Yy]$ ]]; then
      exit 1
    fi
  fi

  # Style Check
  echo "→ Running style compliance check..."
  STYLE=$(claude --print "Review code style for: $CHANGED

Check against common best practices:
1. Consistent naming conventions
2. No magic numbers without constants
3. Functions reasonably sized (<50 lines)
4. Adequate error handling
5. No debug code (console.log, print statements)

Respond concisely:
STYLE: [PASS|WARN]
NOTES: [brief list or 'All good']")

  echo "$STYLE"
  echo ""

done

echo "═══════════════════════════════════════"
echo "  ✓ Audit complete. Proceeding with push."
echo "═══════════════════════════════════════"
```

### Step 2: Make Executable

```bash
chmod +x .git/hooks/pre-push
```

### Step 3: Test the Hook

```bash
# Make a small change and push
echo "// test" >> some-file.js
git add some-file.js
git commit -m "test pre-push hook"
git push
```

The hook should run and show the security/style analysis.

## Lesson 4.2: Team-Wide Deployment

### Option A: Committed Hooks Directory

```bash
# Create a hooks directory in the repo
mkdir -p .githooks

# Copy hooks there
cp .git/hooks/pre-commit .githooks/
cp .git/hooks/pre-push .githooks/

# Add to git
git add .githooks/

# Document in README or CLAUDE.md
```

Team members configure git to use shared hooks:
```bash
git config core.hooksPath .githooks
```

### Option B: npm/package.json Hook Installation

For JavaScript projects, use `husky`:

```json
{
  "scripts": {
    "prepare": "husky install"
  },
  "devDependencies": {
    "husky": "^8.0.0"
  }
}
```

### Option C: Pre-commit Framework

For Python projects, use `pre-commit`:

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: claude-security-scan
        name: Claude Security Scan
        entry: .githooks/pre-commit
        language: script
        pass_filenames: false
```

## Lesson 4.3: Customizing Audit Rules

### Project-Specific Rules

Add to your CLAUDE.md for the hooks to reference:

```markdown
## Security Audit Rules

When auditing this codebase, pay special attention to:
- All user input MUST be sanitized before database queries
- JWT tokens must use RS256 algorithm
- File uploads restricted to: .jpg, .png, .pdf (max 5MB)
- Rate limiting required on all public endpoints

Acceptable patterns:
- Using parameterized queries via our ORM
- Auth middleware from src/middleware/auth.ts

Unacceptable patterns:
- Raw SQL string concatenation
- Storing passwords without bcrypt
- Exposing internal IDs in URLs
```

### Severity Customization

Modify hooks to match your team's risk tolerance:

- **Strict**: Block on MEDIUM and above
- **Standard**: Block on CRITICAL, warn on HIGH
- **Lenient**: Block only on CRITICAL

## Checkpoint

Module 4 is complete when:
- [ ] Pre-commit hook is installed and executable
- [ ] Pre-push hook is installed and executable
- [ ] At least one hook successfully blocked a test commit
- [ ] User understands how to deploy hooks team-wide
- [ ] User knows how to customize audit rules

## Transition

---

**Module 4 Complete!**

You've built automated quality gates that catch issues before they reach your team or production. Every commit is now scanned for secrets, and every push goes through a security audit.

For the final module, you'll learn to capture your best practices and workflows as **reusable Skills** that your entire team can benefit from.

Type "next" or run `/onboard-skills` to continue.

---
