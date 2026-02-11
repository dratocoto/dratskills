---
description: Initialize AI workspace for a project
allowed-tools: Read, Write, Bash(mkdir:*), Bash(tree:*), Bash(ls:*), Bash(cat:*), Bash(grep:*)
argument-hint: [project-name]
---

Initialize the AI Dev Team workspace for the current project.

Read the workflow guide at `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`.
Read the team config at `${CLAUDE_PLUGIN_ROOT}/team.config.yaml`.

## Step 1: Create workspace directory structure

```
.ai-workspace/
├── STATE.md
├── stack.config.yaml     ← Skill resolution (auto-detected, overridable)
├── CONVENTIONS.md
├── CHECKLIST.md
├── features/             ← Each feature gets its own subdirectory
├── discussions/          ← Cross-feature discussions
└── decisions/            ← Shared Architecture Decision Records
```

Note: Feature-specific directories (`features/FEAT-XXX/`) are created by the `/new-feature` command.

## Step 2: Auto-detect tech stack

Read `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/references/stack-profiles.md` for profile definitions.

Run these detection checks (in order):

1. **Check Python backend**:
   - If `pyproject.toml` exists → read it, look for `fastapi`, `django`, `flask` in dependencies
   - Elif `requirements.txt` exists → grep for framework names
   - Record: backend framework + `python312` as language

2. **Check Node.js frontend/backend**:
   - If `package.json` exists → read it, look for `next`, `react`, `vue`, `angular`, `express`, `nestjs` in dependencies
   - Record: frontend/backend framework

3. **Match to profile**:
   - `fastapi` + `next` → profile `fastapi-nextjs`
   - `fastapi` only → profile `fastapi-only`
   - `next` only → profile `nextjs-only`
   - `django` + `react` → profile `django-react`
   - No match → ask human to choose

4. **Create `.ai-workspace/stack.config.yaml`** from the matched profile:
   - Read template: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/stack-config-template.yaml`
   - Fill in: detected stack info, skill mappings from profile
   - Show the human: "Detected stack: [backend] + [frontend]. Skills configured."

## Step 3: Initialize project files

1. Initialize STATE.md from template at `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/state-template.md`
   - Set project name to `$1` or detect from package.json/pyproject.toml
   - Set stack based on detected language

2. Initialize CONVENTIONS.md:
   - Read stack.config.yaml to find the `conventions` skill
   - Read `${CLAUDE_PLUGIN_ROOT}/skills/{conventions_skill}/SKILL.md`
   - Copy the relevant convention sections based on detected stack

3. Create CHECKLIST.md from template at `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/checklist-template.md`

## Step 4: Report and welcome

1. Scan existing project structure with `tree -L 3 src/` (or `app/`, `pages/`) and report:
   - Detected stack
   - Existing modules
   - Current file count

2. Show stack.config.yaml skill mappings:
   ```
   Stack configured (stack.config.yaml):
     backend-patterns → fastapi-patterns
     frontend-guide   → nextjs16-guide
     language          → python312
     conventions       → conventions

   Agent skill assignments (team.config.yaml):
     Architect      → [backend-patterns, frontend-guide, conventions]
     Backend Dev    → [backend-patterns, language, conventions]
     Frontend Dev   → [frontend-guide, conventions]
     Reviewer       → [backend-patterns, frontend-guide, language, conventions]

   To change: edit .ai-workspace/stack.config.yaml (skill resolution)
              edit team.config.yaml (agent assignments)
   ```

3. Display welcome message with available commands and workflow overview.

If the project doesn't exist yet, ask the human which stack to use and offer to scaffold it.

## Re-detecting Stack

If the project's dependencies change (e.g., migrated from React to Next.js), the human can re-run `/start-project` on an existing workspace. The command will:
1. Re-detect the stack from current dependency files
2. Update `.ai-workspace/stack.config.yaml` with new skill mappings
3. Preserve existing STATE.md, features/, and all other workspace files
4. Show a diff of what changed in the skill configuration
