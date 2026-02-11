---
description: Start a new feature (triggers BA for clarification → PM for coordination)
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(ls:*), Bash(tree:*), Bash(mkdir:*)
argument-hint: <feature-description>
---

Start the idea clarification and requirement analysis phase for a new feature.

Read the workflow guide: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`
Read the requirement template: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/requirement-template.md`

## Step 1: PM initializes

1. Read `.ai-workspace/STATE.md` to get the next FEAT-XXX number
2. Create the feature-scoped directory structure:
   ```
   .ai-workspace/features/FEAT-XXX/
   ├── tasks/
   ├── reviews/
   ├── discussions/
   └── handoffs/
   ```
3. Log the feature request in STATE.md Parallel Features table as status: CLARIFY
4. Delegate to BA Agent scoped to `features/FEAT-XXX/`

## Step 2: BA runs Phase 0 — Idea Clarification (MANDATORY)

1. Scan existing codebase structure: `tree -L 2 src/` (if exists) to understand current state

2. Analyze the human's feature description: `$ARGUMENTS`
   - Identify what is CLEAR from the description
   - Identify what is VAGUE or AMBIGUOUS
   - Identify what is MISSING (not mentioned at all)

3. Generate **3-7 clarifying questions** using BA's QA Question Framework:
   - WHO: Who uses this? What roles/permissions?
   - WHAT: Core behavior, data involved, success/error paths?
   - BOUNDARIES: What's out of scope? Limits? Edge cases?
   - INTEGRATION: Connects to existing features? External APIs?
   - UX/BUSINESS: UI preferences? Sorting? Analytics?

   Rules:
   - Lead with the most impactful questions
   - Provide suggested options when possible (e.g., "Auth: JWT or session-based?")
   - Mark optional questions as "Nice to know (optional):"
   - If the feature is very clear → only ask 2-3 essential questions
   - Do NOT overwhelm with 10+ questions

4. Present questions to the human:
   - "Before I write the requirement, I have a few questions to make sure I capture your idea correctly:"
   - [Numbered question list]
   - Wait for human answers

5. If answers reveal NEW ambiguities → ask 1 round of follow-up (max 3 questions)

## Step 3: BA runs Phase 1 — Write Requirement

6. After clarification, create `.ai-workspace/features/FEAT-XXX/requirement.md` with:
   - Problem statement (informed by clarification)
   - User stories derived from the description AND clarification answers
   - Acceptance criteria (specific, testable — incorporating edge cases discussed)
   - Scope estimate (S/M/L/XL based on complexity)
   - Technical constraints (based on existing codebase)
   - Dependencies on existing features
   - **Clarification Log** — full Q&A table for traceability
   - **Key Decisions** — decisions made during clarification
   - **Assumptions** — anything still unclear

## Step 4: PM presents for approval

7. PM updates `.ai-workspace/STATE.md`:
   - Set feature status to REQ (requirement phase)
   - Set current feature

8. PM presents the requirement to the human:
   - Show the structured requirement
   - Highlight key decisions from clarification
   - Ask: "Does this capture what you want? Approve / Modify / Reject"
   - Wait for human response

If approved → PM writes handoff to `features/FEAT-XXX/handoffs/` for architect, updates STATE.md to DESIGN phase.
If rejected → PM sends back to BA with feedback to revise (BA rewrites `features/FEAT-XXX/requirement.md`).
