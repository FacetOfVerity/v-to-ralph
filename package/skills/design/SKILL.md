---
name: design
description: Design system architecture using V-Model principles with BDD-style acceptance criteria.
user-invocable: true
---

# Architect

Design system architecture using V-Model principles with BDD-style acceptance criteria.

## When to Use

Use this skill when starting a new feature or project that needs:
- Clear scope boundaries
- Testable acceptance criteria
- Implementation roadmap with traceability

## Output Files

This skill creates four files in `docs/arch/`:

| File | Purpose |
|------|---------|
| ARCHITECTURE.md | Vision, Boundaries, Ubiquitous Language, Design, Acceptance Criteria |
| DIAGRAMS.md | Sequence diagrams (happy path + complex scenarios) + Class diagrams |
| IMPLEMENTATION_PLAN.md | Tasks with traceability to acceptance criteria |
| INDEX.md | Quick navigation for Ralph Loop |

## Templates

Templates are located at `{CLAUDE_PLUGIN_ROOT}/skills/design/templates/`:
- `ARCHITECTURE.md.template`
- `DIAGRAMS.md.template`
- `IMPLEMENTATION_PLAN.md.template`
- `INDEX.md.template`

Read templates before generating files.

## Execution Flow

### Step 0: Explore Project

**Read-only exploration.** Understand the project before asking questions.

1. **Read existing documentation** (if present):
   - README.md, CLAUDE.md, docs/*.md
   - package.json, go.mod, Cargo.toml, pyproject.toml
   - Any existing architecture docs

2. **Scan codebase structure** (if code exists):
   - Main directories and their purpose
   - Entry points (main.go, index.ts, app.py, etc.)
   - Existing modules/packages

3. **Check `docs/arch/` directory**:
   - If architecture files already exist, note this for Step 1
   - Do NOT overwrite without asking

4. **Detect communication language**:
   - Analyze README, CLAUDE.md, comments to determine project's primary language
   - Use detected language for all user communication

5. **Assess information sufficiency**:
   - Is project purpose clear?
   - Are main features identifiable?
   - Note gaps for Step 1

### Step 1: Gather Requirements

Use `AskUserQuestion` to gather all needed information. Ask questions in logical groups.

**First question block** (always ask):

```
Q1 - "Stack": Which technology stack?
  Options:
  - Go
  - Rust
  - Python
  - Node.js / TypeScript

Q2 - "Docs Language": Which language for architecture documentation?
  Options (order by detected project language):
  - [Detected language] (Recommended - match project)
  - English
  - Russian
  - Other (specify)
  Note: If no language detected, show alphabetically without "Recommended"

Q3 - "Research": Should I research current best practices?
  Options:
  - Yes, research first (recommended for unfamiliar domains)
  - Skip research (use existing knowledge)
```

**Second question block** (conditional):

If Q3 = "Yes, research first", ask separately:
```
Q4 - "Search Tool": Which search tool to use?
  Options: [List available search tools from MCP servers or built-in WebSearch]
```

**Third question block** (always ask):

```
Q5 - "Constraints": Any specific constraints?
  Description: "Performance requirements, integrations, compliance, existing systems..."
  (free text input)
```

**Conditional questions** based on Step 0 findings:

If project purpose is **unclear**:
```
Q - "Project Description": Please describe:
  - What are you building?
  - What problem does it solve?
  - What are the main features?
(free text input)
```

If `docs/arch/` files **already exist**:
```
Q - "Existing Files": Architecture files already exist in docs/arch/. How to proceed?
  Options:
  - Overwrite all (start fresh)
  - Review and update (preserve what's good)
  - Cancel (keep existing)
```

**Terminology rule**: Regardless of documentation language, industry terms remain in English: Ubiquitous Language, Bounded Context, Aggregate Root, Entity, Value Object, Acceptance Criteria, Given/When/Then, etc.

### Step 2: Research (if requested)

If user requested research, use available search tools to gather information.

#### 2.1 Framework Discovery

Search for modern frameworks for the selected stack:
- Best frameworks and libraries
- Project structure conventions
- Community recommendations

#### 2.2 Best Practices Discovery

Search for implementation patterns:
- Error handling approaches
- Testing strategies
- Configuration management
- Security best practices

#### 2.3 Domain-Specific Research (if applicable)

If project has specific domain (fintech, healthcare, IoT):
- Domain architecture patterns
- Compliance requirements
- Industry examples

#### 2.4 Synthesize Findings

Add **Research Summary** section to ARCHITECTURE.md (after Vision, before Boundaries):

```markdown
## Research Summary

### Recommended Stack
| Component | Choice | Rationale |
|-----------|--------|-----------|
| Framework | [Name] v[X.Y] | [Why this over alternatives] |
| Database | [Name] | [Why suitable] |
| Testing | [Framework] | [Why chosen] |

### Key Best Practices
- **Project Structure**: [Convention]
- **Error Handling**: [Pattern]
- **Testing**: [Strategy]

### Sources
- [URL 1] - [What was useful]
- [URL 2] - [What was useful]
```

### Step 3: Generate Architecture Files

Read templates from `{CLAUDE_PLUGIN_ROOT}/skills/design/templates/` then create files in `docs/arch/`:

1. **ARCHITECTURE.md** - Use `ARCHITECTURE.md.template`
2. **DIAGRAMS.md** - Use `DIAGRAMS.md.template`
3. **IMPLEMENTATION_PLAN.md** - Use `IMPLEMENTATION_PLAN.md.template`
4. **INDEX.md** - Use `INDEX.md.template`

**IMPORTANT**: All diagrams MUST use Mermaid format. Never use ASCII art or PlantUML.

### Step 4: Validate

Before presenting to user, verify quality:

**Consistency checks:**
- [ ] All Ubiquitous Language terms are used in Acceptance Criteria
- [ ] All Acceptance Criteria have at least one implementing task
- [ ] All tasks reference at least one Acceptance Criterion (except infrastructure)
- [ ] Diagram participants match defined modules
- [ ] FEATURE-CODEs are consistent across all files

**Quality checks:**
- [ ] No implementation details in GIVEN/WHEN/THEN (no module names)
- [ ] THEN clauses use passive voice (observable outcomes)
- [ ] Each module has single responsibility

If issues found, fix them before proceeding.

### Step 5: Review

Present results to user with `AskUserQuestion`:

```
Architecture documentation created in docs/arch/:

Files:
- ARCHITECTURE.md ([N] lines)
- DIAGRAMS.md ([M] lines)
- IMPLEMENTATION_PLAN.md ([K] lines)
- INDEX.md ([L] lines)

Metrics:
- Domain terms: [X]
- Modules: [Y]
- Acceptance criteria: [Z]
- Implementation tasks: [T]
- Diagrams: [D] sequence + [C] class

Traceability: 100% criteria covered

Q - "Review": How would you like to proceed?
  Options:
  - Proceed to /implement (Recommended)
  - I have feedback (describe changes needed)
  - Start over (regenerate from scratch)
```

If user selects **"I have feedback"**:
- Ask for specific feedback
- Regenerate only affected files
- Return to Step 5

### Step 6: Finalize

After user approves, confirm:

```
Architecture design complete.

Next step: Run /implement to start Ralph Loop
```

## Guidelines

### BDD Rules for Acceptance Criteria

**Strict Rules**:

1. **No Implementation Leaks**: Never mention internal service/module names in Gherkin. Use domain roles or passive voice.

2. **Passive THEN Clauses**: THEN must describe observable state/outcome, not action.
   - BAD: "THEN the Service saves the data"
   - GOOD: "THEN the data is persisted"

3. **Atomic WHEN**: Single trigger action per scenario.

4. **Ubiquitous Language Only**: Use domain terms, not technical terms.

### Class Diagram Guidelines

**When to include**:
- Domain has 3+ entities with relationships
- Aggregate boundaries need clarification
- Value objects vs entities distinction matters

**DDD Annotations**:
- `<<Aggregate Root>>` - Entry point to aggregate
- `<<Entity>>` - Has identity, lifecycle within aggregate
- `<<Value Object>>` - Immutable, no identity
- `<<Repository>>` - Persistence abstraction
- `<<Service>>` - Stateless domain operations

**Relationships**:
- `*--` Composition: Child cannot exist without parent
- `o--` Aggregation: Child can exist independently
- `-->` Association: References

**Multiplicity**: `"1"`, `"0..1"`, `"*"`, `"1..*"`

### Module Granularity

- Each module = one clear responsibility
- If description has "and", consider splitting
- 3-7 modules is typical for medium projects

### Task Traceability

Every task should trace to at least one acceptance criterion.
Every acceptance criterion should be covered by at least one task.
No orphan tasks. No uncovered criteria.
