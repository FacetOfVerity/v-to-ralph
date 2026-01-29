---
name: implement
description: Generate Ralph Loop execution instructions for GS-TDD implementation.
user-invocable: true
---

# Loop Builder

Generate optimized Ralph Loop setup from architecture document. Implements the right branch of V-Model via GS-TDD (Gold Standard TDD).

## When to Use

Use after `/design` has created `docs/arch/ARCHITECTURE.md`.

## Prerequisites

- `/design` has been run, creating:
  - `docs/arch/ARCHITECTURE.md`
  - `docs/arch/DIAGRAMS.md`
  - `docs/arch/IMPLEMENTATION_PLAN.md`
  - `docs/arch/INDEX.md`
- Ralph Loop plugin installed
- Docker running (for Testcontainers)

## Execution Flow

### Step 1: Verify Architecture Exists

```bash
ls docs/arch/ARCHITECTURE.md docs/arch/IMPLEMENTATION_PLAN.md docs/arch/INDEX.md
```

If missing, tell user to run `/design` first.

### Step 2: Parse Architecture Files

Read architecture files and extract:

From `ARCHITECTURE.md`:
- Technology stack (from Constraints or infer from interfaces)
- Modules list

From `INDEX.md`:
- Tasks → Criteria mapping (for RALPH_PROMPT reference)

### Step 3: Create RALPH_PROMPT.md

Create `docs/arch/RALPH_PROMPT.md`:

```markdown
# Ralph Loop: [Project Name]

## Mission

Implement [Project Name] using GS-TDD. All acceptance criteria must pass.

## Architecture Files

```
docs/arch/
├── INDEX.md               # START HERE - task navigation
├── ARCHITECTURE.md        # Criteria, modules, ubiquitous language
├── DIAGRAMS.md            # Sequence diagrams for complex flows
└── IMPLEMENTATION_PLAN.md # MEMORY - progress persists here
```

**IMPLEMENTATION_PLAN.md is your persistent memory:**
- On start: Read to find where you left off
- On task complete: Mark `[x]` and update Traceability Matrix status
- Status flow: ` ` → `In Progress` → `Implemented` → `Tested`

## Rules

1. **Read on-demand** — Use INDEX.md to find relevant sections
2. **GS-TDD** — Comprehensive tests BEFORE code
3. **Verify** — `[build_cmd] && [test_cmd]` after every change
4. **Commit** — After each completed task
5. **Update memory** — Mark `[x]` in tasks, update Status in Traceability Matrix

## Workflow

```
1. Read docs/arch/IMPLEMENTATION_PLAN.md — find next unchecked task
2. Set task's criterion Status=`In Progress` in Traceability Matrix
3. Read task's acceptance criteria in ARCHITECTURE.md
4. If complex flow → check DIAGRAMS.md for sequence diagram
5. Write comprehensive failing tests (RED)
6. Implement production-ready code (GOLD)
7. [build_cmd] && [test_cmd]
8. [format_cmd]
9. git add -A && git commit -m "feat: [task description]"
10. Mark [x] in IMPLEMENTATION_PLAN.md, set Status=`Implemented`
11. Run tests, if pass → set Status=`Tested`
12. Repeat until all tasks complete
```

## GS-TDD: Red → Gold → Refactor

### RED Phase
Write tests covering ALL acceptance criteria for current task:
- Happy path
- Edge cases
- Error conditions
- Boundary values

[Stack-specific test example here]

### GOLD Phase
Write production-ready code (not minimal):
- Proper error handling
- Clean structure
- Following project conventions

### REFACTOR Phase
Only if needed. Tests must stay green.

## Stack: [Language]

```
Build:  [build_cmd]
Test:   [test_cmd]
Format: [format_cmd]
```

### Test Setup

[Stack-specific Testcontainers setup]

## Status Check

Run at each iteration start:

```bash
docker info                    # Testcontainers need Docker
[build_cmd]                    # Build status
[test_cmd]                     # Test status
git log --oneline -5           # Recent progress
grep -c "\- \[x\]" docs/arch/IMPLEMENTATION_PLAN.md  # Completed tasks
```

## Completion

When ALL tasks marked `[x]` AND all tests pass:

```
<promise>PROJECT COMPLETE</promise>
```

## Deliverable Requirement

**CRITICAL**: Before emitting `PROJECT COMPLETE`, the project MUST satisfy ALL conditions:

1. **Fully Buildable**
   - `{build_cmd}` passes without errors
   - All dependencies resolved
   - No compilation warnings (if applicable)

2. **Fully Tested**
   - ALL tests pass (unit + integration)
   - No skipped tests
   - Coverage meets target from TESTS.md (if specified)

3. **All Acceptance Criteria Covered**
   - Every AC from ARCHITECTURE.md has passing tests
   - Traceability Matrix in IMPLEMENTATION_PLAN.md shows all `Tested`

4. **Infrastructure Working** (if applicable)
   - `docker build .` completes without errors
   - `docker-compose up -d` starts full stack
   - All containers show "healthy" status
   - `curl localhost:{port}/healthz` returns 200 with all checks "ok"
   - This is the **final verification** — if `/healthz` passes, the stack works

5. **Production Ready**
   - No TODO comments left unresolved
   - No debug code (console.log, print statements for debugging)
   - Code follows project conventions
   - Clean git history with meaningful commits

**Do NOT emit PROJECT COMPLETE until ALL above conditions are verified.**

## Anti-Patterns

- Reading full ARCHITECTURE.md every iteration (use INDEX.md)
- Writing code before tests
- Leaving tests red between commits
- Skipping acceptance criteria
- Marking task done before tests pass
- Emitting PROJECT COMPLETE with failing tests
- Leaving `:latest` Docker image tags
```

### Step 4: Stack-Specific Configuration

Based on detected stack, use appropriate commands:

#### Go
```yaml
build_cmd: "go build ./..."
test_cmd: "go test ./..."
format_cmd: "go fmt ./... && go vet ./..."
test_example: |
  func TestFeature(t *testing.T) {
      ctx := context.Background()
      container, _ := postgres.Run(ctx, "postgres:16")
      defer container.Terminate(ctx)
      // Test implementation
  }
test_deps: |
  go get github.com/testcontainers/testcontainers-go
  go get github.com/testcontainers/testcontainers-go/modules/postgres
```

#### Rust
```yaml
build_cmd: "cargo build"
test_cmd: "cargo test"
format_cmd: "cargo fmt && cargo clippy"
test_example: |
  #[tokio::test]
  async fn test_feature() {
      let container = GenericContainer::new("postgres:16")
          .start().await;
      // Test implementation
  }
test_deps: |
  cargo add testcontainers --dev
  cargo add tokio --dev --features full
```

#### Python
```yaml
build_cmd: "python -m py_compile **/*.py"
test_cmd: "pytest"
format_cmd: "ruff format . && ruff check --fix ."
test_example: |
  import pytest
  from testcontainers.postgres import PostgresContainer

  def test_feature():
      with PostgresContainer("postgres:16") as postgres:
          # Test implementation
test_deps: |
  pip install pytest pytest-asyncio testcontainers
```

#### Node.js / TypeScript
```yaml
build_cmd: "npm run build"
test_cmd: "npm test"
format_cmd: "npm run lint:fix"
test_example: |
  import { PostgreSqlContainer } from '@testcontainers/postgresql';
  import { describe, it, expect, beforeAll, afterAll } from 'vitest';

  describe('Feature', () => {
    let container;
    beforeAll(async () => {
      container = await new PostgreSqlContainer().start();
    });
    afterAll(async () => {
      await container.stop();
    });
    it('should work', async () => {
      // Test implementation
    });
  });
test_deps: |
  npm install -D vitest testcontainers @testcontainers/postgresql
```

#### .NET / C#
```yaml
build_cmd: "dotnet build"
test_cmd: "dotnet test"
format_cmd: "dotnet format"
test_example: |
  public class FeatureTests : IAsyncLifetime
  {
      private PostgreSqlContainer _postgres;

      public async Task InitializeAsync()
      {
          _postgres = new PostgreSqlBuilder().Build();
          await _postgres.StartAsync();
      }

      public async Task DisposeAsync() => await _postgres.DisposeAsync();

      [Fact]
      public async Task Feature_ShouldWork()
      {
          // Test implementation
      }
  }
test_deps: |
  dotnet add package Testcontainers.PostgreSql
  dotnet add package FluentAssertions
```

### Step 5: Output Result

After creating RALPH_PROMPT.md:

```
Created:
- docs/arch/RALPH_PROMPT.md (execution prompt)

Architecture files (from /design):
- docs/arch/ARCHITECTURE.md
- docs/arch/DIAGRAMS.md
- docs/arch/IMPLEMENTATION_PLAN.md
- docs/arch/INDEX.md

To start implementation:

/ralph-loop "Follow docs/arch/RALPH_PROMPT.md strictly. GS-TDD: tests first. Mark [x] in IMPLEMENTATION_PLAN.md when task complete. When all done: <promise>PROJECT COMPLETE</promise>" --max-iterations 150 --completion-promise "PROJECT COMPLETE"
```

## Token Optimization

Architecture files + INDEX.md save ~70% tokens per iteration:

| Approach | Tokens/Iteration |
|----------|------------------|
| Read full architecture | ~15,000+ |
| INDEX.md + on-demand | ~4,000-6,000 |

Key optimizations:
1. **INDEX.md** (from /architect) — Quick task→criteria lookup
2. **On-demand reading** — Only current task's sections
3. **Compact RALPH_PROMPT.md** — Essential instructions only
