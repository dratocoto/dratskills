# Handoff Protocol

## Why Handoffs Matter

AI agents have limited context windows. When one agent finishes and another takes over,
the new agent starts with a FRESH context. The handoff file bridges this gap by telling
the next agent exactly what happened and what to do next.

## Handoff Schema

Every handoff follows this structure:

```markdown
# Handoff: [Source Agent] → [Target Agent]

## Timestamp: [ISO datetime]
## Feature: FEAT-XXX
## Phase: [current phase]

## What Was Done
- [Bullet list of completed work]
- Files created: [list]
- Files modified: [list]

## What's Needed Next
- [Specific instructions for the next agent]
- [Expected outputs]

## Context for Next Agent (WHAT TO READ)
- File 1: path/to/file — [why to read it]
- File 2: path/to/file — [why to read it]
- Convention: CONVENTIONS.md#section — [specific rules to follow]

## Issues / Blockers
- [Any problems encountered]
- [Questions that need human input, marked with QUESTION:]

## State Update
- STATE.md updated: [yes/no]
- Task card updated: [task ID]
```

## Handoff Examples

### PM → Architect Handoff

```markdown
# Handoff: PM → Architect

## Feature: FEAT-003 Product Catalog
## Phase: REQUIREMENT → DESIGN

## What Was Done
- Requirement doc created and approved by human
- Scope: L (estimated 6-8 files)

## What's Needed Next
- Design system architecture for product catalog
- Define data models, API endpoints, file structure
- Break into implementation tasks (≤ 3 files each)

## Context for Next Agent
- features/FEAT-003/requirement.md — the approved requirement
- Run: tree -L 3 src/ — understand current project structure
- src/models/ — existing model patterns
- src/api/v1/routes/ — existing route patterns
- CONVENTIONS.md — coding standards

## Issues / Blockers
- None
```

### Architect → PM → Dev Handoff

```markdown
# Handoff: Architect → Backend Dev (via PM)

## Feature: FEAT-003 Product Catalog
## Phase: DESIGN → IMPLEMENT (Task 1 of 4)

## What Was Done
- Design spec completed: features/FEAT-003/design.md
- Design approved by human
- 4 implementation tasks created

## What's Needed Next
- Implement TASK-012: Product model and schemas
- Create SQLAlchemy model and Pydantic schemas

## Context for Next Agent
- features/FEAT-003/tasks/TASK-012.md — the task card with all instructions
- CONVENTIONS.md#python-models — model naming rules
- CONVENTIONS.md#pydantic — schema patterns
- features/FEAT-003/design.md#data-models — field definitions

## Issues / Blockers
- None
```

### Dev → Test Handoff

```markdown
# Handoff: Backend Dev → Test Agent

## Feature: FEAT-003 Product Catalog
## Phase: IMPLEMENT → TEST (Tasks 1-4 complete)

## What Was Done
- All 4 implementation tasks completed
- Files created:
  - src/models/product.py
  - src/schemas/product.py
  - src/repositories/product_repo.py
  - src/services/product_service.py
  - src/api/v1/routes/products.py
- Self-check passed on all files

## What's Needed Next
- Unit tests for ProductRepository (mock DB)
- Unit tests for ProductService (mock repo)
- Unit tests for Pydantic schemas (validation cases)
- Integration tests for API endpoints (test client)

## Context for Next Agent
- features/FEAT-003/design.md#api-endpoints — expected behavior
- src/models/product.py — model definition
- src/repositories/product_repo.py — methods to test
- src/services/product_service.py — business logic to test
- CONVENTIONS.md#testing — test patterns and naming

## Issues / Blockers
- QUESTION: Should integration tests use real DB or in-memory?
  → PM should ask human
```

### QA → PM Handoff

```markdown
# Handoff: QA → PM

## Feature: FEAT-003 Product Catalog
## Phase: REVIEW → [APPROVED or NEEDS_CHANGES]

## What Was Done
- Full code review completed
- Review report: features/FEAT-003/reviews/qa-report.md

## Summary
- Verdict: NEEDS_CHANGES
- Critical: 1 (missing input validation on update endpoint)
- Warnings: 2 (missing structured logging in service layer)
- Suggestions: 3

## What's Needed Next
- Fix critical issue in src/api/v1/routes/products.py:45
- Fix warnings in src/services/product_service.py
- Re-run QA after fixes

## Context for PM
- features/FEAT-003/reviews/qa-report.md — full review details
- Present to human for final decision
```

## Handoff File Naming

The active handoff is always `HANDOFF-latest.md`. When a new handoff is written, the previous one is archived:

```
features/FEAT-XXX/handoffs/
├── HANDOFF-latest.md              ← Current (PM reads this)
├── HANDOFF-001-ba-to-architect.md ← Archived: Phase 0→2
├── HANDOFF-002-arch-to-dev.md     ← Archived: Phase 2→3
└── HANDOFF-003-dev-to-test.md     ← Archived: Phase 3→5
```

**Naming convention for archives**: `HANDOFF-{seq}-{source}-to-{target}.md`

PM should rename `HANDOFF-latest.md` to the archive name BEFORE writing the new one. This preserves history while keeping the "latest" pointer stable for agents that always read `HANDOFF-latest.md`.

## Handoff Rules

1. **Always update STATE.md** before writing handoff
2. **Be explicit about files** — list exact paths, never "the relevant files"
3. **Limit context references** — max 5-7 files for the next agent
4. **Mark questions clearly** — prefix with `QUESTION:` for PM to escalate
5. **Include self-check results** — did the agent validate its own output?
6. **Never assume** — if something is unclear, flag it rather than guess
7. **Archive before overwrite** — rename HANDOFF-latest.md before writing a new one
