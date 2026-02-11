---
description: Generate tests for implemented code
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(pytest:*), Bash(python3:*), Bash(npm:*), Bash(npx:*), Bash(ls:*), Bash(tree:*)
argument-hint: [feature-id]
---

Generate comprehensive tests for a feature's implementation.

Read the workflow guide: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`

1. Read `.ai-workspace/STATE.md` to identify the target feature (or use `$1` as FEAT-XXX)

2. Read `${CLAUDE_PLUGIN_ROOT}/team.config.yaml` → find `test-agent` → get skill categories
   Read `.ai-workspace/stack.config.yaml` → resolve categories to skill names → load each skill

3. Read `.ai-workspace/features/FEAT-XXX/handoffs/HANDOFF-latest.md` for list of implemented files

4. Read the design for expected behavior: `.ai-workspace/features/FEAT-XXX/design.md`

5. For each implemented source file:
   a. Read the source file to understand methods, types, error cases
   b. Generate test file with:
   - Happy path tests (valid input → expected output)
   - Edge case tests (boundary values, empty input)
   - Error case tests (invalid input → expected error)
   - Schema validation tests
     c. Follow Arrange-Act-Assert pattern
     d. Use descriptive test names: `test_<unit>_<scenario>_<expected>`

6. Create shared fixtures if not exists

7. Run tests using the appropriate runner from stack.config.yaml:
   - Python: `pytest tests/ -v --tb=short`
   - TypeScript: `npx vitest --run`

8. Run coverage:
   - Python: `pytest tests/ --cov=src --cov-report=term-missing -q`
   - TypeScript: `npx vitest --coverage`

9. Write handoff to `.ai-workspace/features/FEAT-XXX/handoffs/HANDOFF-latest.md`:
   - Test results (passed/failed)
   - Coverage per module
   - Any failing tests with details
   - Recommendation: proceed to QA or fix issues first

10. PM updates `.ai-workspace/STATE.md` for FEAT-XXX to TEST phase

If all tests pass and coverage ≥ 80% → suggest moving to QA phase.
If tests fail → report failures and suggest fixes.
