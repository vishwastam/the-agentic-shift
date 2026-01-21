# Module 3: Test-Driven Agentic (TDA) Development

## Learning Objectives

By the end of this module, learners will:
- Understand the philosophy: "Tests are your specification language"
- Write failing tests that define desired behavior
- Prompt Claude effectively to implement solutions
- Iterate based on test output until all tests pass

## The TDA Philosophy

### Teaching Points

Explain:

1. **Traditional TDD**: Write test → Write code → Refactor
2. **Agentic TDA**: Write test → Prompt Claude → Review & iterate

The key insight: **Your tests become your prompts.** Instead of describing what you want in natural language, you express it in executable specifications.

### Why TDA Works

| Aspect | Natural Language Prompt | Test-Based Specification |
|--------|------------------------|--------------------------|
| Precision | "Handle edge cases" | `expect(fn(null)).toThrow()` |
| Verification | Manual review | Automated pass/fail |
| Iteration | Unclear what failed | Exact failure location |
| Documentation | Lost after generation | Tests persist as specs |

## The TDA Cycle

```
    ┌─────────────────────────────────────────┐
    │                                         │
    │   1. WRITE        2. PROMPT             │
    │      TEST    →       CLAUDE             │
    │   [Human]         [Agent]               │
    │                                         │
    │        ↑              ↓                 │
    │                                         │
    │   4. REFINE  ←    3. RUN                │
    │      PROMPT        TESTS                │
    │   [If failing]    [Automated]           │
    │                                         │
    └─────────────────────────────────────────┘
```

### Step 1: WRITE TEST (Human)
You define WHAT the code should do, not HOW.

### Step 2: PROMPT CLAUDE (Agent)
Claude implements code to satisfy your specification.

### Step 3: RUN TESTS (Automated)
Tests provide objective feedback on correctness.

### Step 4: REFINE PROMPT (If needed)
If tests fail, the error output guides your next prompt.

## Lesson 3.1: Detect Project Test Framework

### Discovery

First, identify what testing framework they use:

```bash
# Node.js projects
grep -l "jest\|vitest\|mocha" package.json 2>/dev/null && echo "Found JS test framework"

# Python projects
test -f pytest.ini && echo "pytest"
test -f setup.cfg && grep -q pytest setup.cfg && echo "pytest in setup.cfg"
grep -l "pytest\|unittest" requirements*.txt 2>/dev/null && echo "Found Python test framework"

# Go projects
ls *_test.go 2>/dev/null && echo "Go tests present"

# Rust projects
grep -q "\[dev-dependencies\]" Cargo.toml 2>/dev/null && echo "Rust tests available"
```

If no test framework exists, help them set one up:

**JavaScript/TypeScript**:
```bash
npm install -D vitest
```

**Python**:
```bash
pip install pytest
```

## Lesson 3.2: Writing Effective Test Specifications

### Principles

1. **Test behavior, not implementation**
   - Good: `"returns user object with correct properties"`
   - Bad: `"calls database.query with specific SQL"`

2. **One assertion per test** (when starting out)
   - Easier to identify what failed
   - Clearer prompts for Claude

3. **Name tests as specifications**
   - Good: `it('rejects passwords shorter than 8 characters')`
   - Bad: `it('test1')`

4. **Include edge cases**
   - Null inputs
   - Empty strings/arrays
   - Boundary values

### Example: Good Test Suite

```typescript
describe('validateEmail', () => {
  it('returns true for valid email addresses', () => {
    expect(validateEmail('user@example.com')).toBe(true);
    expect(validateEmail('name.surname@domain.co.uk')).toBe(true);
  });

  it('returns false for emails without @ symbol', () => {
    expect(validateEmail('userexample.com')).toBe(false);
  });

  it('returns false for emails without domain', () => {
    expect(validateEmail('user@')).toBe(false);
  });

  it('returns false for empty string', () => {
    expect(validateEmail('')).toBe(false);
  });

  it('returns false for null or undefined', () => {
    expect(validateEmail(null)).toBe(false);
    expect(validateEmail(undefined)).toBe(false);
  });
});
```

## Interactive Exercise 3.1: Your First TDA Cycle

### Scenario Selection

Ask the learner what they'd like to build. Offer options based on their project:

**Option A: Utility Function**
A string manipulation or data transformation function

**Option B: Validation Logic**
Input validation for a form or API

**Option C: Business Logic**
A domain-specific calculation or rule

**Option D: API Helper**
A function that processes API responses

### Exercise: Build a Slug Generator

If they don't have a preference, use this universal example:

**Step 1: Write the Tests**

Guide them to create this test file:

```typescript
// tests/slug.test.ts (or .js, .py, etc.)
import { describe, it, expect } from 'vitest';
import { generateSlug } from '../src/slug';

describe('generateSlug', () => {
  it('converts text to lowercase', () => {
    expect(generateSlug('Hello World')).toBe('hello-world');
  });

  it('replaces spaces with hyphens', () => {
    expect(generateSlug('my blog post')).toBe('my-blog-post');
  });

  it('removes special characters', () => {
    expect(generateSlug('Hello! How are you?')).toBe('hello-how-are-you');
  });

  it('collapses multiple hyphens into one', () => {
    expect(generateSlug('hello   world')).toBe('hello-world');
  });

  it('trims leading and trailing hyphens', () => {
    expect(generateSlug('  hello world  ')).toBe('hello-world');
  });

  it('handles empty string', () => {
    expect(generateSlug('')).toBe('');
  });

  it('handles strings with only special characters', () => {
    expect(generateSlug('!@#$%')).toBe('');
  });
});
```

**Step 2: Run Tests (Confirm Failure)**

```bash
npm test -- tests/slug.test.ts
# or
pytest tests/test_slug.py
```

Expected: All tests fail (module doesn't exist yet).

**Step 3: Prompt Claude**

Teach the optimal prompt structure:

```
Make the tests in tests/slug.test.ts pass.

Create src/slug.ts (or appropriate path) with a generateSlug function.

Requirements from the tests:
- Convert to lowercase
- Replace spaces with hyphens
- Remove special characters (keep only alphanumeric and hyphens)
- Collapse multiple consecutive hyphens
- Trim leading/trailing hyphens

Follow the coding patterns in this project.
```

**Step 4: Run Tests Again**

```bash
npm test -- tests/slug.test.ts
```

If all pass: Celebrate and analyze the generated code.

If some fail: Move to Step 5.

**Step 5: Iterate (If Needed)**

When tests fail, the output guides the next prompt:

```
The test "handles strings with only special characters" is failing.

Expected: ''
Received: '-'

Update generateSlug to return empty string when input
contains only special characters.
```

### Debrief

After completing the cycle, discuss:

1. How did the tests guide Claude's implementation?
2. What would have happened without tests?
3. How does this compare to describing requirements in English?

## Lesson 3.3: Advanced TDA Patterns

### Pattern: Incremental Complexity

Start simple, add tests one at a time:

```
Round 1: "Make the first test pass"
Round 2: "Now also make the second test pass without breaking the first"
Round 3: Continue...
```

### Pattern: Contract Testing

For APIs or integrations:

```typescript
describe('UserAPI', () => {
  it('GET /users/:id returns user shape', async () => {
    const response = await api.get('/users/1');
    expect(response).toMatchObject({
      id: expect.any(Number),
      email: expect.any(String),
      createdAt: expect.any(String),
    });
  });
});
```

### Pattern: Behavior Specification

For complex business logic:

```typescript
describe('PricingCalculator', () => {
  describe('when user has premium subscription', () => {
    it('applies 20% discount', () => { /* ... */ });
    it('waives shipping fee', () => { /* ... */ });
  });

  describe('when user has basic subscription', () => {
    it('applies 5% discount', () => { /* ... */ });
    it('charges shipping based on weight', () => { /* ... */ });
  });
});
```

## Lesson 3.4: When TDA Doesn't Fit

Be honest about limitations:

| Scenario | Better Approach |
|----------|-----------------|
| UI/UX work | Visual review, Playwright tests |
| Exploratory coding | Prototype first, test later |
| Performance tuning | Benchmarks, not unit tests |
| Integration issues | Manual debugging, logs |

TDA works best for **deterministic logic with clear inputs and outputs**.

## Checkpoint

Module 3 is complete when:
- [ ] User has written at least one test file with 3+ tests
- [ ] Tests initially failed (proving they're real specifications)
- [ ] Claude successfully generated code that passes all tests
- [ ] User completed at least one iteration cycle (test failed → refined prompt → test passed)
- [ ] User can explain why tests are better than natural language specs

## Transition

---

**Module 3 Complete!**

You've mastered the core workflow of agentic development. Writing tests first gives you:
- Precise specifications
- Automated verification
- Clear iteration feedback

Now let's ensure quality at scale with **Module 4: Governance** - automated quality gates that catch issues before they reach production.

Type "next" or run `/onboard-governance` to continue.

---
