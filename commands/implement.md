---
description: Implement the next task card
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(python3:*), Bash(ruff:*), Bash(mypy:*), Bash(npm:*), Bash(npx:*), Bash(ls:*)
argument-hint: [task-id]
---

Implement a specific task from the current feature.

Read the workflow guide: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`

1. Read `.ai-workspace/STATE.md` to find the target feature and current task (or use `$1` as FEAT-XXX/TASK-XXX)

2. Read `${CLAUDE_PLUGIN_ROOT}/team.config.yaml` and `.ai-workspace/stack.config.yaml` to determine tech stack

3. Read the task card: `.ai-workspace/features/FEAT-XXX/tasks/TASK-XXX.md`

4. Check the task label:
   - `backend` → route to **Backend Dev** agent
   - `frontend` → route to **Frontend Dev** agent
   - `full-stack` → Backend Dev first, then Frontend Dev

5. The Dev agent will:
   a. Load skills from team.config.yaml + stack.config.yaml
   b. Read ONLY the files listed in the task card's "Files to Read" section
   c. Write code following conventions and framework patterns
   d. Self-validate (linter, type-check)
   e. Update task card status to DONE
   f. Write `.ai-workspace/features/FEAT-XXX/handoffs/HANDOFF-latest.md`

6. PM updates `.ai-workspace/STATE.md` with progress for FEAT-XXX

7. Trigger Reviewer for peer code review

8. If Reviewer approves and more tasks remain → show progress and ask human if they want to continue
   If all tasks done → suggest moving to test phase
