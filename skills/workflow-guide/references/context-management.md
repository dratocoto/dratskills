# Context Management Strategy

## The Core Problem

AI agents have limited context windows. A typical project may have hundreds of files.
Loading everything = diluted attention = poor code quality.

**Solution**: File-based shared memory + task-scoped context loading.

## Principle: "Load Only What You Need"

Each agent interaction should load the MINIMUM files needed to complete its task.

### Context Budget per Agent

| Agent | Budget | Breakdown |
|-------|--------|-----------|
| PM | ~3 files | STATE.md + 1 requirement + 1 task card |
| Architect | ~5 files | requirement + tree output + 2-3 existing patterns |
| Backend Dev | ~5 files | task card + resolved skills + conventions section + 2-3 source files |
| Frontend Dev | ~5 files | task card + resolved skills + conventions section + 2-3 source files |
| Test | ~5 files | spec section + 2-3 source files to test + conventions |
| QA | ~7 files | conventions + checklist + source files + test files |

### How to Stay Within Budget

**1. Structured References, Not Full Reads**

Instead of loading an entire CONVENTIONS.md, reference specific sections:

```
Read: CONVENTIONS.md#python-models   ← Just the models section
NOT:  CONVENTIONS.md                 ← The entire file
```

Use markdown headers as section anchors. Each agent reads only relevant sections.

**2. Task Cards as Context Containers**

The task card IS the context. It contains:
- What to do (instructions)
- What to read (file references with reasons)
- What to produce (expected outputs)
- How to validate (acceptance criteria)

The agent reads the task card → reads referenced files → works → done.

**3. Compact Document Formats**

CONVENTIONS.md uses checklist format, not prose:

```markdown
## Python Models
- [ ] Use SQLAlchemy declarative base
- [ ] UUID primary keys (import from uuid)
- [ ] created_at, updated_at as default columns
- [ ] Type hints on all columns
- [ ] Table name = lowercase plural (users, products)
- [ ] Relationship lazy loading = "selectin"
```

This is ~6 lines vs a 2-page prose explanation. Same information, 10x less tokens.

**4. Progressive Disclosure Through File Hierarchy**

```
Level 0: CONVENTIONS.md         ← Always loaded (~200 lines, compact)
Level 1: features/FEAT-XXX/design.md ← Loaded for the specific feature
Level 2: references/patterns.md ← Loaded only when agent needs deep detail
```

Each agent loads Level 0 + relevant Level 1. Level 2 only on demand.

## STATE.md as Single Source of Truth

STATE.md is the "kanban board" that every agent reads first:

```markdown
# Project State

## Active Feature: FEAT-003 Product Catalog
## Current Phase: IMPLEMENTATION
## Current Task: TASK-014 (3 of 4)

## Feature Progress
| Feature | Requirement | Design | Implement | Test | Review |
|---------|------------|--------|-----------|------|--------|
| FEAT-001 Auth | ✅ | ✅ | ✅ | ✅ | ✅ DONE |
| FEAT-002 Users | ✅ | ✅ | ✅ | ✅ | 🔄 IN REVIEW |
| FEAT-003 Products | ✅ | ✅ | 🔄 3/4 | ⏳ | ⏳ |
| FEAT-004 Orders | ✅ | ⏳ | ⏳ | ⏳ | ⏳ |

## Recent Activity
- [2025-01-15 14:30] TASK-013 completed by backend-dev-agent
- [2025-01-15 14:00] TASK-012 completed by backend-dev-agent
- [2025-01-15 12:00] Design approved for FEAT-003
```

STATE.md rules:
- Max 50 lines (summary only)
- Updated after every task completion
- Read by PM at start of every interaction
- Never deleted, only appended/updated

## Avoiding Context Rot

**Context rot** = performance degrades as conversation gets longer.

Prevention strategies:

**1. One Task Per Agent Invocation**
Don't ask the Dev to implement 5 tasks in one go. One task, one invocation.

**2. Fresh Start for Each Task**
Each agent interaction starts fresh:
- Read task card → Read referenced files → Do work → Write output → Done

**3. Summarize, Don't Accumulate**
After completing a phase, write a summary (handoff) rather than carrying
the full history forward.

**4. Split Large Features**
If a feature requires > 10 files, split into sub-features.
Each sub-feature goes through the full pipeline independently.

## File Size Limits

| File Type | Max Lines | If Exceeded |
|-----------|----------|-------------|
| CONVENTIONS.md | 200 | Split into sections, use references/ |
| STATE.md | 50 | Archive old features to history/ |
| Task card | 40 | Break into smaller tasks |
| Requirement doc | 60 | Use bullet points, not prose |
| Design spec | 100 | Move details to references/ |
| Handoff | 30 | Be concise, reference files |

## Anti-Patterns to Avoid

1. **Loading the entire codebase** — Never. Use tree + targeted reads.
2. **Prose-heavy documentation** — Use tables, checklists, bullet points.
3. **Carrying conversation history** — Each agent starts fresh with file context.
4. **One giant task card** — Break into 3-file-max tasks.
5. **Duplicating information** — Reference files, don't copy content.
6. **Skipping STATE.md updates** — The team loses coordination.
