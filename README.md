# v-to-ralph

V-Model implementation pipeline for Claude Code. Design architecture with testable acceptance criteria, then execute autonomous implementation via Ralph Loop.

## Overview

This plugin implements the [V-Model](https://en.wikipedia.org/wiki/V-model_(software_development)) of software development:

```mermaid
flowchart LR
    subgraph LEFT["/design"]
        A1[Vision & Scope] --> A2[System Design]
        A2 --> A3[Acceptance Criteria]
        A3 --> A4[Implementation Plan]
    end

    subgraph BRIDGE["/implement"]
        B1[RALPH_PROMPT.md]
    end

    subgraph RIGHT["/ralph-loop"]
        R1[RED: Tests] --> R2[GOLD: Code]
        R2 --> R3[REFACTOR]
        R3 -.->|next task| R1
    end

    A4 --> B1
    B1 --> R1
```

- **Left branch** (`/design`): Design with BDD acceptance criteria
- **Bridge** (`/implement`): Generate execution prompt
- **Right branch** (`/ralph-loop`): GS-TDD implementation with [Ralph Wiggum loop](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum)

## Why V-Model?

The left branch of the V-Model is essentially **waterfall** — detailed upfront design before implementation. For human developers, this is often impractical: requirements change, designs become outdated, and the rigidity slows iteration.

**But for AI agents, it's ideal:**

| Aspect | Human Problem | AI Agent Advantage |
|--------|---------------|-------------------|
| Upfront design | Feels like overhead | Provides clear execution context |
| Detailed specs | Become stale quickly | Agent executes immediately, no staleness |
| Rigid structure | Limits adaptability | Reduces ambiguity, fewer hallucinations |
| Acceptance criteria | Extra documentation burden | Machine-checkable success criteria |

**The key insight**: What makes waterfall painful for humans (extensive planning before coding) is exactly what makes AI agents effective (unambiguous instructions with verifiable outcomes).

The right branch then leverages another AI strength: **tireless, systematic execution**. An agent can run hundreds of test iterations without fatigue, methodically working through each criterion until all tests pass.

## How It Works

```mermaid
sequenceDiagram
    participant U as User
    participant A as /design
    participant L as /implement
    participant R as /ralph-loop

    U->>A: Describe project
    A->>U: Q&A (stack, constraints)
    A-->>U: 4 architecture docs

    U->>L: /implement
    L-->>U: RALPH_PROMPT.md

    U->>R: /ralph-loop
    loop For each task
        R->>R: RED - Write tests
        R->>R: GOLD - Implement
        R->>R: Commit
    end
    R-->>U: PROJECT COMPLETE
```

## Output Files

```mermaid
graph TD
    subgraph docs/arch/
        ARCH[ARCHITECTURE.md]
        DIAG[DIAGRAMS.md]
        PLAN[IMPLEMENTATION_PLAN.md]
        INFRA[INFRASTRUCTURE.md]
        STRUCT[PROJECT_STRUCTURE.md]
        TESTS[TESTS.md]
        INDEX[INDEX.md]
        RALPH[RALPH_PROMPT.md]
    end

    ARCH --> INDEX
    DIAG --> INDEX
    PLAN --> INDEX
    INFRA --> INDEX
    STRUCT --> INDEX
    TESTS --> INDEX
    INDEX --> RALPH
```

| File | Content |
|------|---------|
| `ARCHITECTURE.md` | Vision, boundaries, ubiquitous language, system design, acceptance criteria |
| `DIAGRAMS.md` | Sequence diagrams (happy path + complex scenarios) |
| `IMPLEMENTATION_PLAN.md` | Persistent memory — phased tasks, progress tracking |
| `INFRASTRUCTURE.md` | Docker compose configuration, required services |
| `PROJECT_STRUCTURE.md` | Directory layout, module mapping |
| `TESTS.md` | Test specification, test stack, testing philosophy |
| `INDEX.md` | Navigation, tasks-to-criteria mapping |
| `RALPH_PROMPT.md` | Stack-specific GS-TDD execution instructions |

## Quick Start

```bash
# 1. Design architecture
/design "Build a CLI tool for managing Docker containers"

# 2. Generate loop setup
/implement

# 3. Execute implementation
/ralph-loop
```

## Usage Example

**Minimal setup**: Create a `README.md` with your project description — that's enough.
The agent scans the environment (existing code, configs, dependencies) and adapts.

```
project/
└── README.md   ← Just describe what you want to build
```

**Interactive Q&A**: During `/design`, the agent asks clarifying questions:

```
┌─ Stack ───────────────────────────────────────────────────┐
│ Which technology stack?                                   │
│ ○ Go                                                      │
│ ○ Rust                                                    │
│ ○ Python                                                  │
│ ○ Other                                                   │
└───────────────────────────────────────────────────────────┘

┌─ Database ────────────────────────────────────────────────┐
│ Which database?                                           │
│ ○ PostgreSQL (Recommended)                                │
│ ○ SQLite                                                  │
│ ○ None                                                    │
│ ○ Other                                                   │
└───────────────────────────────────────────────────────────┘
```

**Optional research**: The agent can search for best practices, library docs,
and patterns for your stack before generating architecture.

**Output**: After Q&A, the agent generates 7 architecture documents in `docs/arch/`
with acceptance criteria ready for GS-TDD implementation.

## GS-TDD

Gold-Standard Test-Driven Development replaces classic Red-Green-Refactor:

```mermaid
graph LR
    R((RED)) -->|Write failing tests| G((GOLD))
    G -->|Implement production code| T{Tests pass?}
    T -->|Yes| RF((REFACTOR))
    T -->|No| G
    RF -->|Next task| R
```

| Phase | Classic TDD | GS-TDD |
|-------|-------------|--------|
| RED | Minimal failing test | Comprehensive tests covering ALL criteria |
| GREEN/GOLD | Make it pass (any code) | Production-ready code from start |
| REFACTOR | Clean up | Only if needed, tests stay green |

**The key difference**: Classic TDD uses "baby steps" — write one minimal test,
make it pass with minimal code, refactor, repeat. This works well for humans
thinking incrementally.

GS-TDD uses "batch testing" — write ALL tests for the current task upfront
(covering all acceptance criteria), then implement production-quality code.
For AI agents, tests aren't just verification — they're the complete specification
that survives across iterations.

**Why GS-TDD for AI agents?**
- Tests serve as persistent memory across iterations
- Comprehensive coverage prevents regression
- No throwaway code that needs rewriting

## Installation

TODO describe

## Project Structure

```
v-to-ralph/
├── package/
│   ├── plugin.json                   # Plugin manifest
│   └── skills/
│       ├── design/
│       │   ├── SKILL.md              # /design implementation
│       │   ├── validation-rules.md   # Validation rules for design outputs
│       │   └── templates/            # Output file templates
│       │       ├── ARCHITECTURE.md.template
│       │       ├── DIAGRAMS.md.template
│       │       ├── IMPLEMENTATION_PLAN.md.template
│       │       ├── INDEX.md.template
│       │       ├── INFRASTRUCTURE.md.template
│       │       ├── PROJECT_STRUCTURE.md.template
│       │       └── TESTS.md.template
│       └── implement/
│           └── SKILL.md              # /implement implementation
├── README.md
├── CLAUDE.md
└── LICENSE
```

## Requirements

- [ralph-wiggum](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum) plugin (for `/ralph-loop`)
- Docker (for Testcontainers)

## License

MIT
