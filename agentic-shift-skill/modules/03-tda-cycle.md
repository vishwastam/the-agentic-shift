# Module 3: Specification-Driven Development

## Learning Objectives

By the end of this module, learners will:
- Write effective specifications (PRDs, user stories, or feature briefs)
- Guide Claude through context gathering
- Review Claude-generated tests before implementation
- Validate generated code through automated testing

## The New Workflow

### Old Way (Manual)
```
Developer writes code → Developer writes tests → Debug → Repeat
```

### Agentic Way
```
Specify → Context → Generate Tests → Generate Code → Validate
  [You]    [Claude]    [Claude]        [Claude]       [You]
```

**Your job**: Define WHAT you want
**Claude's job**: Figure out HOW, with your approval at checkpoints

## The Specification-Driven Cycle

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  1. SPECIFY          2. CONTEXT         3. GENERATE         │
│     (You write)         (Claude asks)      (Claude writes)  │
│                                                             │
│  "I need a user       "What auth do      Tests first,       │
│   registration        you use? Where     then code to       │
│   endpoint..."        do users live?"    pass them          │
│                                                             │
│         │                  │                   │            │
│         └────────┬─────────┴───────────────────┘            │
│                  ▼                                          │
│           4. VALIDATE                                       │
│              (You review)                                   │
│                                                             │
│           Run tests, review code,                           │
│           approve or iterate                                │
│                                                             │
└─────────────────────────────────────────────────────────────┘
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

## Step 3: Test Generation

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

## Step 4: Code Generation & Validation

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
1. Propose tests based on the requirements
2. Wait for my approval
3. Implement code to pass the tests
```

### Step 3: Answer Context Questions

Claude asks, you answer with real details about your project.

### Step 4: Review Generated Tests

Claude shows tests. You approve or adjust.

### Step 5: Validate Implementation

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
- [ ] Claude generated tests from your spec
- [ ] Tests pass with Claude's implementation

## Key Takeaway

**You are the architect. You define what should exist.**

Claude handles:
- Asking the right questions
- Writing tests from requirements
- Implementing to pass tests
- Iterating on feedback

Your job is specification and validation, not implementation.
