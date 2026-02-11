# Workflow Phases — Detailed Guide

## Phase 0: Idea Clarification (BA Agent)

### Purpose
The BA asks targeted QA questions to clear up the human's idea BEFORE writing any formal requirement. This prevents building the wrong thing and saves the whole team's time.

### Input
Human's initial feature description (can be vague, one-liner, or detailed).

### Process
1. Read STATE.md to understand existing features and project context
2. Scan codebase structure (if exists) to understand technical landscape
3. Analyze the human's request:
   - **CLEAR**: What is explicitly stated and unambiguous
   - **VAGUE**: What is mentioned but needs more detail
   - **MISSING**: What is NOT mentioned but needed for implementation
4. Generate 3-7 clarifying questions using the QA Question Framework
5. Present questions to human → wait for answers
6. If answers reveal NEW ambiguities → 1 follow-up round (max 3 questions)

### QA Question Framework

Questions are organized by category. PM selects the most relevant ones — NOT all of them:

**Category 1: WHO (Users & Roles)**
- Who will use this feature?
- Are there different user roles or permission levels?
- Is authentication/authorization required?

**Category 2: WHAT (Core Behavior)**
- What is the main action or flow? (step by step)
- What data goes in? What comes out?
- What does "success" look like?
- What should happen on failure or invalid input?
- Is there a specific response format needed?

**Category 3: BOUNDARIES (Scope & Edge Cases)**
- What is explicitly NOT included in this feature?
- Are there any limits? (max records, file size, rate limits)
- What happens with empty, null, or zero data?
- Do we need to handle concurrent access?
- Should this be idempotent?

**Category 4: INTEGRATION (Technical Context)**
- Does this connect to existing features? Which ones?
- External APIs or services involved?
- Real-time needs? (WebSocket, SSE, polling)
- Database changes required? (new tables, migrations)

**Category 5: UX & BUSINESS (If applicable)**
- Is there a UI component? Any preferred patterns?
- Sorting, filtering, pagination requirements?
- Analytics or event tracking needed?
- Performance expectations? (response time, throughput)

### Rules for Effective Questioning
1. Ask **3-7 questions** per round — quality over quantity
2. Lead with the **most impactful** questions first
3. Provide **suggested options** when possible:
   - "Auth approach: JWT tokens or session cookies?"
   - "Error format: RFC 7807 Problem Details or custom JSON?"
4. Group related questions together
5. Mark non-essential questions as **"Nice to know (optional):"**
6. If the feature request is already detailed → only ask **2-3 confirming** questions
7. **Max 2 rounds** of Q&A — don't interrogate endlessly

### Example Interaction

```
Human: "I want to add user authentication to the API"

BA: "Before I write the requirement, let me clarify a few things:

1. **Authentication method**: JWT tokens (stateless) or session-based (server-side)?
2. **User registration**: Should users self-register, or admin-only creation?
3. **Password rules**: Any specific requirements? (min length, complexity, etc.)
4. **OAuth/social login**: Just email+password, or also Google/GitHub login?
5. **Authorization**: Are there different roles (admin, user, viewer)?

Nice to know (optional):
6. Email verification required for new accounts?
7. Password reset flow needed in this iteration?"

```

### Output
No formal output file — the clarification is conversational. But the Q&A is captured in the requirement doc's "Clarification Log" section for traceability.

### Transition to Phase 1
Once BA has enough clarity → immediately proceed to write the requirement doc. No separate checkpoint needed — the clarification IS the checkpoint.

---

## Phase 1: Requirement Analysis (BA Agent)

### Input
Clarified feature idea (from Phase 0 Q&A) + human's original request.

### Process
1. Synthesize the original request + clarification answers into a coherent requirement
2. Identify: WHO needs it, WHAT it does, WHY it's needed (now with clear answers)
3. Break into user stories with acceptance criteria (edge cases from Q&A included)
4. Identify technical constraints and dependencies
5. Estimate scope: S (1-2 files), M (3-5 files), L (6-10 files), XL (10+ files)
6. Include the full Clarification Log in the requirement doc

### Output: `features/FEAT-XXX/requirement.md`

```markdown
# FEAT-XXX: [Feature Name]

## Status: DRAFT → APPROVED
## Scope: M (estimated 4-5 files)
## Priority: HIGH

## Problem Statement
[1-2 sentences: what problem does this solve?]

## User Stories
1. As a [role], I want to [action] so that [benefit]
2. ...

## Acceptance Criteria
- [ ] AC-1: [specific, testable criterion]
- [ ] AC-2: ...

## Technical Constraints
- Must use async for all DB operations
- Must follow existing auth middleware pattern
- API must be backward compatible

## Out of Scope
- [What this feature does NOT include]

## Dependencies
- Depends on: [existing modules/features]
- Blocks: [features waiting on this]

## Clarification Log
Questions asked and answers received during Phase 0:

| # | Question | Human's Answer |
|---|----------|---------------|
| 1 | Auth method: JWT or session? | JWT stateless |
| 2 | Self-registration or admin-only? | Self-register with email verification |
| 3 | Password rules? | Min 8 chars, 1 uppercase, 1 number |
| 4 | OAuth needed? | Not in this iteration |
| 5 | User roles? | Admin + regular user |
```

### Checkpoint: REQ-APPROVE
BA hands the requirement doc back to PM. PM presents it to the human:
- "BA has prepared the requirement based on your discussion. Here it is:"
- "Scope estimate is M (4-5 files). Does that match your expectation?"
- Highlight key decisions made during clarification
- Human approves, modifies, or rejects.

---

## Phase 2: Technical Design (Architect Agent)

### Input
Approved requirement doc + existing codebase structure.

### Process
1. Read the requirement doc
2. Scan existing codebase: `tree -L 3 src/` to understand structure
3. Read key interface files (models, routers, services) to understand patterns
4. Design components, data flow, API contracts
5. Break into implementation tasks (each task ≤ 3 files)

### Output: `features/FEAT-XXX/design.md`

```markdown
# FEAT-XXX Design: [Feature Name]

## Architecture Overview
[Mermaid diagram showing component relationships]

## Data Models

### NewModel
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| id | UUID | auto | Primary key |
| name | str | yes | Display name |

## API Endpoints

### POST /api/v1/resource
- Auth: Required (Bearer token)
- Request Body: CreateResourceSchema
- Response 201: ResourceResponse
- Response 400: ValidationError
- Response 401: UnauthorizedError

### GET /api/v1/resource/{id}
...

## File Changes

| File | Action | Description |
|------|--------|-------------|
| src/models/resource.py | CREATE | SQLAlchemy model |
| src/schemas/resource.py | CREATE | Pydantic schemas |
| src/repositories/resource_repo.py | CREATE | Data access layer |
| src/services/resource_service.py | CREATE | Business logic |
| src/api/v1/routes/resource.py | CREATE | API endpoints |

## Implementation Tasks

### TASK-001: Data Models and Schemas
- Create: src/models/resource.py, src/schemas/resource.py
- Read: CONVENTIONS.md#python-models

### TASK-002: Repository Layer
- Create: src/repositories/resource_repo.py
- Read: src/repositories/base.py (follow pattern)

### TASK-003: Service Layer
- Create: src/services/resource_service.py
- Read: TASK-001 output files

### TASK-004: API Endpoints
- Create: src/api/v1/routes/resource.py
- Read: src/api/v1/routes/ (follow existing pattern)
- Modify: src/api/v1/router.py (register new route)

## Design Decisions
- [Why this approach over alternatives]
```

### Checkpoint: DESIGN-APPROVE
Architect presents to human via PM:
- Component diagram
- File list with descriptions
- Task breakdown
- Human approves the architecture or suggests changes.

---

## Phase 3: Implementation (Backend Dev / Frontend Dev, task by task)

### Input
One task card at a time. Dev NEVER loads the full design — only the task card.

### Process (per task)
1. Read task card for scope and instructions
2. Read referenced convention rules
3. Read existing files it needs to follow/modify
4. Write code following conventions
5. Self-validate against CHECKLIST.md
6. Update task status

### Task Card: `features/FEAT-XXX/tasks/TASK-XXX.md`

```markdown
# TASK-XXX: [Task Title]

## Feature: FEAT-XXX
## Agent: backend-dev-agent / frontend-dev-agent
## Label: backend / frontend / full-stack
## Status: TODO → IN_PROGRESS → DONE

## What to Do
[Clear, actionable description]

## Files to Read (Context)
- CONVENTIONS.md#python-models (naming and patterns)
- src/repositories/base.py (follow this pattern)
- features/FEAT-XXX/design.md#data-models (field definitions)

## Files to Create/Modify
- CREATE: src/models/resource.py
- CREATE: src/schemas/resource.py

## Acceptance Criteria
1. Model has all fields from the design spec
2. Pydantic schemas for Create, Update, Response
3. Type hints on all functions
4. Async-compatible

## Self-Check Before Done
- [ ] Type hints complete
- [ ] No hardcoded values
- [ ] Error handling present
- [ ] Follows naming conventions
- [ ] No TODO/FIXME left
```

### Dev Rules
- ONLY read files listed in the task card
- Load skills via team.config.yaml + stack.config.yaml (backend-patterns or frontend-guide)
- ALWAYS follow CONVENTIONS.md patterns
- ALWAYS self-check before marking done
- If uncertain about a design decision → add `# QUESTION:` comment and flag to PM

---

## Phase 4: Testing (Test Agent)

### Input
Completed code files + relevant spec sections.

### Process
1. Read the spec section for expected behavior
2. Read the implementation code
3. Generate tests following testing conventions
4. Run tests and report results
5. If coverage < 80% for business logic → generate more tests

### Output
- Test files in `tests/`
- Coverage report in handoff

### Test-to-Code Mapping
For each source file, the Test Agent creates:
- `tests/unit/test_<module>.py` — unit tests with mocked dependencies
- `tests/integration/test_<module>_integration.py` — tests with real DB (if applicable)

---

## Phase 5: Review (QA Agent)

### Input
All new/modified source files + test files + conventions.

### Process
1. Read CONVENTIONS.md and CHECKLIST.md
2. For each source file, check:
   - Code quality (naming, structure, complexity)
   - Security (OWASP patterns)
   - Error handling (completeness)
   - Type safety (annotations)
   - Test coverage (are edge cases tested?)
3. Produce structured review report

### Output: `features/FEAT-XXX/reviews/qa-report.md`

```markdown
# Review: FEAT-XXX [Feature Name]

## Verdict: APPROVED / NEEDS_CHANGES / REJECTED

## Summary
- Files reviewed: N
- Critical issues: N
- Warnings: N
- Suggestions: N

## Critical (must fix)
1. [file:line] — Description → Fix suggestion

## Warnings (should fix)
1. [file:line] — Description → Fix suggestion

## Suggestions (nice to have)
1. [file:line] — Description → Improvement idea

## Test Coverage Assessment
- Business logic coverage: X%
- Missing test cases: [list]

## Production Readiness
- [ ] No hardcoded secrets
- [ ] All inputs validated
- [ ] Errors handled properly
- [ ] Logging present (structured)
- [ ] No TODO/FIXME in production paths
```

### Final Checkpoint: QA-APPROVE
PM presents review to human. Human makes final decision.
