# Module 1: Foundation - CLAUDE.md and Project Initialization

## Learning Objectives

By the end of this module, learners will:
- Understand why project context dramatically improves Claude's assistance
- Successfully run `/init` to scaffold Claude Code configuration
- Create a comprehensive CLAUDE.md tailored to their project

## Prerequisites Check

Before starting, verify:
1. User is in a project directory (has source code)
2. Directory is a git repository (or guide them to init one)
3. Claude Code CLI is authenticated

```bash
# Check git
git status > /dev/null 2>&1 && echo "Git: OK" || echo "Git: Not initialized"

# Check for existing CLAUDE.md
test -f CLAUDE.md && echo "CLAUDE.md: Already exists" || echo "CLAUDE.md: Needs creation"

# Check for .claude directory
test -d .claude && echo ".claude/: Exists" || echo ".claude/: Needs creation"
```

## Lesson 1.1: Why Context Matters

### Teaching Points

Explain to the learner:

1. **Without context**, Claude makes generic suggestions that may not fit the project
2. **With CLAUDE.md**, Claude understands:
   - Project architecture and key components
   - Team coding conventions and patterns
   - Build commands and development workflow
   - What to do AND what to avoid

### Demonstration

Show them the difference by having Claude answer a question about their codebase:
- First, without any context (generic response)
- Then, after creating CLAUDE.md (tailored response)

## Lesson 1.2: Running /init

### Steps

1. If `.claude/` doesn't exist, guide them to run:
   ```bash
   claude /init
   ```

2. Explain what was created:
   - `.claude/settings.json` - Project-specific Claude configuration
   - May scaffold a basic `CLAUDE.md` template

3. If `/init` was already run, acknowledge and move to CLAUDE.md creation.

## Lesson 1.3: CLAUDE.md Structure

### Required Sections

Guide them through creating each section:

```markdown
# Project Name

One-line description of what this project does.

## Overview

2-3 paragraphs explaining:
- The project's purpose
- Key features
- Target users/use case

## Architecture

Describe the high-level structure:
- Main directories and their purposes
- Key components and how they interact
- Data flow through the system

## Tech Stack

List technologies used:
- Language and version
- Framework(s)
- Database
- Key dependencies

## Getting Started

```bash
# Install dependencies
<command>

# Run development server
<command>

# Run tests
<command>

# Build for production
<command>
```

## Code Conventions

List team standards:
- Naming conventions (camelCase, snake_case, etc.)
- File organization patterns
- Error handling approach
- Testing requirements

## Key Patterns

Describe patterns Claude should follow:
- How services are structured
- How API endpoints are defined
- How database queries are written
- How tests are organized

## Do NOT

Critical constraints for Claude:
- Files/directories never to modify
- Patterns to avoid
- Security-sensitive areas
- Deprecated approaches not to use
```

## Interactive Exercise 1.1: Build Your CLAUDE.md

### Instruction to Claude

Guide the user through these questions, then generate their CLAUDE.md:

**Discovery Questions:**

1. "What is your project called and what does it do in one sentence?"

2. "What's your primary programming language and framework?"

3. "Describe your directory structure. What are the main folders and what's in them?"

4. "What commands do you run most often for development?"
   - Installing dependencies
   - Running the dev server
   - Running tests
   - Building/deploying

5. "What coding conventions does your team follow?"
   - Naming (variables, files, classes)
   - Formatting (tabs/spaces, line length)
   - Documentation requirements

6. "Are there any patterns that should ALWAYS be followed in this codebase?"
   - Error handling style
   - API response format
   - Database access patterns

7. "What should Claude NEVER do in this project?"
   - Protected files/folders
   - Deprecated patterns
   - Security-sensitive operations

### Generation

After gathering answers, generate a complete CLAUDE.md and write it to the project root.

## Verification

After creating CLAUDE.md, verify:

```bash
# Check file exists
test -f CLAUDE.md && echo "✓ CLAUDE.md created"

# Check file has content
[ $(wc -l < CLAUDE.md) -gt 20 ] && echo "✓ CLAUDE.md has substantial content"

# Check key sections exist
grep -q "## Overview" CLAUDE.md && echo "✓ Has Overview section"
grep -q "## Architecture" CLAUDE.md && echo "✓ Has Architecture section"
grep -q "## Getting Started" CLAUDE.md && echo "✓ Has Getting Started section"
grep -q "## Do NOT" CLAUDE.md && echo "✓ Has constraints section"
```

## Checkpoint

Module 1 is complete when:
- [ ] `/init` has been run (`.claude/` directory exists)
- [ ] `CLAUDE.md` exists in project root
- [ ] CLAUDE.md contains at least: Overview, Architecture, Getting Started, Conventions, Do NOT sections
- [ ] User can explain why context improves Claude's responses

## Transition

Once verified, congratulate them and transition:

---

**Module 1 Complete!**

You've created the foundation for effective AI-assisted development. Your CLAUDE.md gives Claude the context it needs to provide relevant, project-specific assistance.

Ready to supercharge Claude with real-time data from your tools? Let's move to **Module 2: MCP Mastery**.

Type "next" or run `/onboard-mcp` to continue.

---
