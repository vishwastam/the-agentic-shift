# Module 3: Specification-Driven Development

## Learning Objectives

By the end of this module, learners will:
- Write effective specifications (PRDs, user stories, or feature briefs)
- Guide Claude through context gathering
- Review and approve Claude's execution plan
- Review Claude-generated tests before implementation
- Validate generated code through automated testing

## The New Workflow

### Old Way (Manual)
```
Developer writes code → Developer writes tests → Debug → Repeat
```

### Agentic Way
```
Specify → Context → Plan → Generate Tests → Generate Code → Validate
  [You]   [Claude]  [Interactive]  [Claude]    [Claude]       [You]
```

**Your job**: Define WHAT you want
**Claude's job**: Figure out HOW, with your approval at checkpoints

## The Specification-Driven Cycle

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  1. SPECIFY       2. CONTEXT        3. PLAN        4. GENERATE  │
│     (You)            (Claude)          (Interactive)  (Claude)  │
│                                                                 │
│  "I need user      "What auth?      "Here's my      Tests first │
│   registration"    Where do users   plan..."        then code   │
│                    live?"           [You approve]               │
│                                                                 │
│         │              │                 │               │      │
│         └──────────────┴─────────────────┴───────────────┘      │
│                                 ▼                               │
│                          5. VALIDATE                            │
│                             (You review)                        │
│                                                                 │
│                    Run tests, review code,                      │
│                    approve or iterate                           │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Step 1: Write a Specification

A good spec answers: **What do I want to exist that doesn't exist yet?**

### Spec Formats (Pick One)

**User Story Format:**
```markdown
As a [user type]
I want to [action]
So that [benefit]

Acceptance Criteria:
- [ ] Criterion 1
- [ ] Criterion 2
```

**Feature Brief Format:**
```markdown
## Feature: [Name]

### Problem
What problem does this solve?

### Solution
What should happen?

### Requirements
- Requirement 1
- Requirement 2

### Out of Scope
- What this does NOT include
```

**Simple Format:**
```markdown
## [Feature Name]

Build a function/endpoint/component that:
- Does X
- Handles Y
- Returns Z

Constraints:
- Must work with existing [system]
- Should not break [thing]
```

### Example Spec

```markdown
## Feature: User Registration API

### Problem
Users can't create accounts in our system.

### Solution
Create a POST /api/users endpoint that registers new users.

### Requirements
- Accept email and password
- Validate email format
- Require password minimum 8 characters
- Hash password before storing
- Return user object (without password)
- Return 400 for validation errors
- Return 409 if email already exists

### Out of Scope
- Email verification (separate feature)
- OAuth/social login
```

## Step 2: Context Gathering

After you provide a spec, Claude should ask clarifying questions. **Answer honestly** - this is where tribal knowledge gets captured.

### Questions Claude Should Ask

```
Based on your spec, I have some questions:

1. Architecture: Where does this fit?
   - Which directory/module?
   - Any existing patterns to follow?

2. Dependencies: What already exists?
   - Database/ORM you're using?
   - Existing auth/validation utilities?
   - Error handling conventions?

3. Constraints: What should I avoid?
   - Protected files?
   - Deprecated patterns?

4. Testing: How do you test?
   - Test framework?
   - Where do tests live?
   - Any test utilities?
```

### Example Context Exchange

**You**: [paste spec from above]

**Claude**: "I have some questions:
1. What database/ORM do you use for users?
2. Where should this endpoint live?
3. What's your password hashing approach?
4. What does your error response format look like?"

**You**: "We use Prisma with PostgreSQL. Put it in src/routes/users.ts. Use bcrypt for passwords. Errors are { error: string, code: number }."

Now Claude has real context, not assumptions.

## Step 3: Execution Planning

After gathering context, Claude enters **plan mode** to create a detailed execution plan. This is an interactive checkpoint where you refine the approach before any code is written.

### What Claude Presents

```
Based on your spec and context, here's my execution plan:

## Execution Plan: User Registration API

### Files to Create/Modify
1. `src/routes/users.ts` - New file for user registration endpoint
2. `src/routes/users.test.ts` - Test file
3. `src/routes/index.ts` - Add route import

### Implementation Steps
1. Create Prisma schema update for User model (if needed)
2. Write test cases covering all requirements
3. Implement POST /api/users endpoint
4. Add email validation using existing validator
5. Integrate bcrypt for password hashing
6. Add error handling following your { error, code } pattern

### Dependencies
- Using existing: `bcrypt`, `prisma`, email validator from `src/utils/validators`
- No new dependencies needed

### Questions Before Proceeding
1. Should I run `prisma migrate` after schema changes?
2. Do you want integration tests with a real DB or mocked?

Does this plan look right? Any adjustments?
```

### Your Review Checkpoint

At this stage, you can:
- **Approve**: "Looks good, proceed"
- **Adjust scope**: "Skip the Prisma migration, schema already exists"
- **Add constraints**: "Use the test database for integration tests"
- **Request changes**: "Also add rate limiting to the endpoint"

### Example Plan Interaction

**Claude**: "Here's my plan: [shows plan above]. Does this look right?"

**You**: "Two changes: 1) Schema already exists, skip that step. 2) Add a test for rate limiting - max 5 requests per minute per IP."

**Claude**: "Got it. Updated plan:
- Removed Prisma schema step
- Added rate limiting test case
- Will use existing `rateLimiter` middleware from `src/middleware/`

Ready to generate tests?"

**You**: "Yes, proceed."

### Why Plan Mode Matters

- **Catches misunderstandings early** - Before code is written
- **Surfaces hidden dependencies** - Claude might reference files you forgot about
- **Gives you control** - You approve the approach, not just the output
- **Saves iteration cycles** - Better plan = fewer rewrites

## Step 4: Test Generation

Claude proposes tests based on your spec. **Review them before implementation.**

### What Claude Should Generate

```typescript
// Claude generates tests from your requirements:

describe('POST /api/users', () => {
  it('creates user with valid email and password', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', password: 'password123' });

    expect(res.status).toBe(201);
    expect(res.body.email).toBe('test@example.com');
    expect(res.body.password).toBeUndefined(); // Not exposed
  });

  it('returns 400 for invalid email', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ email: 'invalid', password: 'password123' });

    expect(res.status).toBe(400);
  });

  it('returns 400 for short password', async () => {
    const res = await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', password: '123' });

    expect(res.status).toBe(400);
  });

  it('returns 409 for duplicate email', async () => {
    // Create user first, then try again
    await createUser({ email: 'test@example.com' });

    const res = await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', password: 'password123' });

    expect(res.status).toBe(409);
  });
});
```

### Your Review Checkpoint

Before Claude implements, review the tests:
- Do they cover all requirements?
- Are edge cases handled?
- Do they match your testing patterns?

Say "yes" to proceed, or "add test for X" to expand.

## Step 5: Code Generation & Validation

Claude implements code to pass the tests, then you validate.

### The Loop

```
Claude: "Here's the implementation. Running tests..."
Claude: "4/4 tests passing. Here's the code: [shows diff]"

You: Review the diff
  - Does it follow your patterns?
  - Any security concerns?
  - Approve or request changes

You: "Looks good" or "Change X to Y"
```

## Exercise: Your First Spec-Driven Feature

### Step 1: Write Your Spec

Pick something real for your project, or use this:

```markdown
## Feature: Slug Generator Utility

### Problem
Need to convert titles to URL-friendly slugs.

### Requirements
- Convert to lowercase
- Replace spaces with hyphens
- Remove special characters
- Handle empty strings

### Example
"Hello World!" → "hello-world"
```

### Step 2: Give Spec to Claude

```
Here's my feature spec:

[paste spec]

Ask me any clarifying questions about my codebase, then:
1. Create an execution plan for my approval
2. Propose tests based on the requirements
3. Wait for my approval
4. Implement code to pass the tests
```

### Step 3: Answer Context Questions

Claude asks, you answer with real details about your project.

### Step 4: Review Execution Plan

Claude presents a plan. You approve, adjust, or add constraints.

### Step 5: Review Generated Tests

Claude shows tests. You approve or adjust.

### Step 6: Validate Implementation

Tests run, you review the code, done.

## When This Works Best

| Good Fit | Less Ideal |
|----------|------------|
| New features with clear requirements | Exploratory/research work |
| Bug fixes (spec = "this should work") | UI/visual work |
| API endpoints | Performance optimization |
| Utility functions | Complex refactoring |
| Business logic | |

## Checkpoint

Module 3 is complete when:
- [ ] You've written a spec for a feature
- [ ] Claude asked context questions and you answered
- [ ] Claude presented an execution plan and you approved it
- [ ] Claude generated tests from your spec
- [ ] Tests pass with Claude's implementation

## Key Takeaway

**You are the architect. You define what should exist.**

Claude handles:
- Asking the right questions
- Creating detailed execution plans
- Writing tests from requirements
- Implementing to pass tests
- Iterating on feedback

Your job is specification, plan approval, and validation—not implementation.
