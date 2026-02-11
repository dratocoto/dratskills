---
description: Create technical design for current feature
allowed-tools: Read, Write, Glob, Grep, Bash(ls:*), Bash(tree:*)
argument-hint: [feature-id]
---

Trigger the technical design phase for a feature.

Read the workflow guide: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`
Read the design template: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/design-spec-template.md`

1. Read `.ai-workspace/STATE.md` to identify the target feature (or use `$1` as FEAT-XXX)

2. Read `${CLAUDE_PLUGIN_ROOT}/team.config.yaml` → find `architect-agent` → get skill categories
   Read `.ai-workspace/stack.config.yaml` → resolve categories to skill names → load each skill

3. Read the approved requirement: `.ai-workspace/features/FEAT-XXX/requirement.md`

4. Analyze the existing codebase:
   - Run `tree -L 3 src/` to understand structure
   - Read 2-3 existing pattern files to follow conventions
   - Read `.ai-workspace/CONVENTIONS.md` for coding rules

5. Create `.ai-workspace/features/FEAT-XXX/design.md` with:
   - Architecture overview (Mermaid diagram)
   - Data models (table format)
   - API endpoints (with request/response schemas)
   - **File plan** (every file to create/modify — used for File Conflict Map)
   - Implementation tasks (ordered, ≤ 3 files each, **labeled `backend` or `frontend`**)
   - Design decisions with rationale

6. PM updates STATE.md:
   - Set FEAT-XXX phase to DESIGN
   - Update **File Conflict Map** with files from the design's file plan
   - Check for conflicts with other parallel features

7. Present the design to human:
   - Show component diagram
   - Show file plan
   - Show task breakdown (with backend/frontend labels)
   - If file conflicts detected → highlight and propose resolution
   - Ask: "Approve this design? Approve / Modify / Reject"

If approved → create task cards in `features/FEAT-XXX/tasks/` and move to IMPLEMENT phase.
