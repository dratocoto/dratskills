---
description: Show current project and all parallel feature statuses
allowed-tools: Read, Bash(tree:*), Bash(wc:*)
---

Display the current project status from the AI Dev Team workspace.

1. Read `.ai-workspace/STATE.md` and display the **Parallel Features** table

2. For each active feature, also show:
   - Current phase and what's next
   - Task progress (e.g., "3 of 5 tasks completed")
   - Any pending discussions or blockers

3. Check root `.ai-workspace/discussions/` for cross-feature conflicts

4. Show a summary:
   ```
   ## Project Status

   | Feature | Phase | Progress | Status |
   |---------|-------|----------|--------|
   | FEAT-001 Auth | IMPL [3/5] | 60% | IN_PROGRESS |
   | FEAT-002 Catalog | DESIGN | 30% | WAITING_HUMAN |
   | FEAT-003 Orders | CLARIFY | 10% | IN_PROGRESS |

   Active discussions: 1 (cross-feature file conflict)
   Next recommended action: [suggestion]
   ```

5. If file conflicts exist in STATE.md's File Conflict Map, highlight them

6. If `.ai-workspace/` doesn't exist, inform the human:
   "No AI workspace found. Run `/start-project` to initialize."
