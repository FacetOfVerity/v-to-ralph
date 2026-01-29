# CLAUDE.md

**Note**: All project documentation is maintained in English.

## Philosophy

**Problem**: AI agents lose context across iterations. They write code that passes immediate tests but breaks existing functionality. Without persistent memory, each iteration starts almost from scratch.

**Solution**: Tests as persistent memory.

```
Traditional workflow:
  Human describes → AI writes code → Human reviews → repeat
  (context lost between iterations)

V-to-Ralph workflow:
  Design once → Tests encode requirements → AI implements against tests
  (tests ARE the persistent context)
```

**Core insight**: In GS-TDD, tests aren't just verification — they're specification. When an AI agent runs `go test` and sees failures, it knows exactly what to fix. No ambiguity, no context loss.

**V-Model mapping**:
- Left branch = human thinking (what to build)
- Right branch = machine verification (proof it works)
- Bridge = translation from human intent to machine-checkable criteria

**Design principles**:
1. **Testable over documented** — acceptance criteria must be executable
2. **Traceable over comprehensive** — every task links to criteria, no orphans
3. **Production-ready over incremental** — GOLD phase writes real code, not stubs
4. **Autonomous over interactive** — ralph-loop runs without human intervention

## Project Overview

**v-to-ralph** is a Claude Code plugin implementing V-Model software development:
- `/design` — Left branch: design with BDD acceptance criteria
- `/implement` — Bridge to right branch: GS-TDD via Ralph Loop

## Architecture

```
v-to-ralph/
├── package/
│   ├── plugin.json                   # Plugin manifest
│   └── skills/
│       ├── design/
│       │   ├── SKILL.md              # /design implementation
│       │   └── templates/            # Output file templates
│       │       ├── ARCHITECTURE.md.template
│       │       ├── DIAGRAMS.md.template
│       │       ├── IMPLEMENTATION_PLAN.md.template
│       │       └── INDEX.md.template
│       └── implement/
│           └── SKILL.md              # /implement implementation
├── README.md
└── CLAUDE.md
```

## Key Concepts

### Ubiquitous Language (from DDD)

Shared vocabulary used consistently across all documents and code.
Defined in ARCHITECTURE.md, referenced everywhere.

### Acceptance Criteria Format (BDD)

```
GIVEN [context using ubiquitous language terms]
WHEN [actor performs action]
THEN [observable outcome - passive voice, no module names]
```

Rule: Gherkin describes business behavior (WHAT), not implementation (HOW).

### Sequence Diagrams

- Happy path: typical successful flows
- Complex scenarios: error handling, retries, edge cases

### Class Diagrams

- Domain entities with properties and types
- Relationships with multiplicity
- DDD annotations (Aggregate Root, Entity, Value Object)

### Traceability

Tasks reference criteria: `Covers: AUTH-AC1, AUTH-AC2`
Every criterion must be covered. No orphans.

### GS-TDD

Red → Gold → Refactor (not Red → Green → Refactor):
- RED: Comprehensive tests covering ALL criteria
- GOLD: Production-ready code from start
- REFACTOR: Only if needed, tests stay green

## Output Files

When skills run, they create:

```
docs/arch/
├── ARCHITECTURE.md        # /design: Vision, Boundaries, Ubiquitous Language, Design, Criteria
├── DIAGRAMS.md            # /design: Sequence + Class diagrams
├── IMPLEMENTATION_PLAN.md # /design: Persistent memory — tasks, progress, traceability
├── INDEX.md               # /design: Navigation index
└── RALPH_PROMPT.md        # /implement: Execution instructions
```

**IMPLEMENTATION_PLAN.md as memory**: Ralph Loop reads this file to find where it left off and updates it after each task. The Traceability Matrix tracks status (` ` → `In Progress` → `Implemented` → `Tested`) for each acceptance criterion.

## Development Notes

- Skills use `AskUserQuestion` for interactive Q&A
- Research phase is optional (user choice)
- Search tool is configurable (WebSearch, perplexity-mcp, etc.)
- Designed for powerful LLMs (Claude Opus) — minimal guardrails
