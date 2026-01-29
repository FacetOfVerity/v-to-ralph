# Design Validation Rules

Reference document for validation checks in the /design skill.

## BDD Rules

Rules for writing Acceptance Criteria in Given/When/Then format:

### 1. No Implementation Leaks

Never mention internal service/module names in Gherkin. Use domain roles or passive voice.

**Bad:**
```
GIVEN the UserService is initialized
WHEN the AuthController receives a login request
THEN the SessionManager creates a session
```

**Good:**
```
GIVEN a registered user exists
WHEN valid credentials are submitted
THEN a session is created
```

### 2. Passive THEN Clauses

THEN must describe observable state/outcome, not action.

**Bad:**
- "THEN the Service saves the data"
- "THEN the system sends an email"
- "THEN the handler returns 200"

**Good:**
- "THEN the data is persisted"
- "THEN an email is sent"
- "THEN a success response is returned"

### 3. Atomic WHEN

Single trigger action per scenario. If you have multiple WHENs, split into multiple criteria.

**Bad:**
```
WHEN the user clicks submit AND the form is validated
```

**Good:**
```
WHEN the user submits the form
```

### 4. Ubiquitous Language Only

Use domain terms from the Ubiquitous Language section, not technical terms.

**Bad:**
- "GIVEN a row in the users table"
- "WHEN POST /api/orders is called"
- "THEN the JSON response contains..."

**Good:**
- "GIVEN a registered customer"
- "WHEN an order is placed"
- "THEN the order confirmation is returned"

### 5. Context in GIVEN

GIVEN describes the initial context using domain terms. It sets up the preconditions.

**Good examples:**
- "GIVEN an authenticated user with admin role"
- "GIVEN an order in 'pending' status"
- "GIVEN the daily limit has been reached"

## Traceability Rules

Rules ensuring complete coverage between criteria and tasks:

### 1. Every Criterion Must Be Covered

Every acceptance criterion in ARCHITECTURE.md must be referenced by at least one task in IMPLEMENTATION_PLAN.md.

**Check**: Count criteria in ARCHITECTURE.md, verify all appear in task "Covers:" fields.

### 2. Every Task Must Reference Criteria

Every task (except Phase 0 infrastructure tasks) must reference at least one acceptance criterion.

**Exception**: Tasks in Phase 0 (Test Infrastructure Setup) and Phase 1 (Foundation) may cover "Infrastructure" instead of specific criteria.

### 3. No Orphan Tasks

Tasks without criterion coverage indicate scope creep or missing criteria.

**If found**: Either remove the task or add corresponding acceptance criteria.

### 4. No Uncovered Criteria

Criteria without implementing tasks indicate incomplete planning.

**If found**: Add tasks to cover the missing criteria.

## Consistency Rules

Rules ensuring coherence across all generated documents:

### 1. Ubiquitous Language Alignment

All terms used in Acceptance Criteria must be defined in the Ubiquitous Language section.

**Check**: Extract nouns from all GIVEN/WHEN/THEN clauses, verify each appears in Ubiquitous Language table.

### 2. Diagram-Language Alignment

All entities in class diagrams and participants in sequence diagrams must match Ubiquitous Language terms.

**Check**: Extract participant/class names from DIAGRAMS.md, verify each is in Ubiquitous Language.

### 3. FEATURE-CODE Consistency

Feature codes (e.g., AUTH-AC1, ORDER-AC2) must be consistent across:
- ARCHITECTURE.md (definition)
- IMPLEMENTATION_PLAN.md (task references)
- TESTS.md (test specifications)
- INDEX.md (mapping tables)

**Check**: Extract all FEATURE-AC patterns, verify identical usage across files.

### 4. Test File-Task Alignment

Test file paths in TESTS.md must correspond to tasks in IMPLEMENTATION_PLAN.md.

**Check**: Each test specification should reference a task, and vice versa.

## Validation Checklist

Quick reference for Step 4 validation:

```
[ ] All Ubiquitous Language terms used in criteria
[ ] All criteria have implementing tasks
[ ] All tasks reference criteria (except infrastructure)
[ ] Diagram participants match Ubiquitous Language
[ ] FEATURE-CODEs consistent across files
[ ] No implementation details in GIVEN/WHEN/THEN
[ ] THEN clauses use passive voice
[ ] Each module has single responsibility
[ ] Every AC has test specification
[ ] Test stack matches selected framework
[ ] All commands valid for framework
[ ] Coverage matrix lists ALL criteria
[ ] Docker images have pinned versions (no :latest)
[ ] Health checks defined for services
[ ] No unreplaced {placeholder} patterns
```
