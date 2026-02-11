---
description: Run QA review on a feature
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(ruff:*), Bash(mypy:*), Bash(pytest:*), Bash(npx:*), Bash(tree:*)
argument-hint: [feature-id]
---

Run a comprehensive QA review on a feature (final quality gate).

Read the workflow guide: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`

1. Read `.ai-workspace/STATE.md` to identify the target feature (or use `$1` as FEAT-XXX)

2. Read `${CLAUDE_PLUGIN_ROOT}/team.config.yaml` → find `qa-agent` → get skill categories
   Read `.ai-workspace/stack.config.yaml` → resolve categories to skill names → load each skill

3. Read `.ai-workspace/features/FEAT-XXX/handoffs/HANDOFF-latest.md` for file list
4. Read `.ai-workspace/features/FEAT-XXX/requirement.md` for acceptance criteria
5. Read `.ai-workspace/features/FEAT-XXX/design.md` for expected architecture
6. Read `.ai-workspace/CONVENTIONS.md` for coding rules

7. Run automated checks based on stack.config.yaml:
   - Python: `ruff check src/`, `mypy src/`, `pytest --cov -q`
   - TypeScript: `npx tsc --noEmit`, `npx eslint src/`, `npx vitest --coverage`

8. For each source file in the feature, review:

   **Acceptance Criteria:**
   - Each AC from the requirement doc → PASS/FAIL

   **Security:**
   - Input validation present
   - No hardcoded secrets
   - Auth on protected routes
   - No stack traces in responses

   **Production Readiness:**
   - Structured logging (not print/console.log)
   - No debug code or commented-out code
   - Config externalized
   - Error responses consistent

9. Write QA report: `.ai-workspace/features/FEAT-XXX/reviews/qa-report.md`
   Format: See QA agent prompt for template

10. PM updates `.ai-workspace/STATE.md` for FEAT-XXX to QA phase

11. Present findings to human:
   - Verdict: APPROVED / NEEDS_CHANGES / REJECTED
   - Critical issues (if any)
   - Warnings count
   - Coverage percentage
   - Ask: "Approve to merge? Or should we fix issues first?"
