# Stack Profiles

Pre-defined skill mappings for common tech stacks.
Used by `/start-project` to auto-generate `.ai-workspace/stack.config.yaml`.

## Profile: fastapi-nextjs (default)

| Category | Skill Name |
|----------|-----------|
| backend-patterns | fastapi-patterns |
| frontend-guide | nextjs16-guide |
| language | python312 |
| conventions | conventions |

**Detection**: `pyproject.toml` contains `fastapi` AND `package.json` contains `next`

## Profile: fastapi-only

| Category | Skill Name |
|----------|-----------|
| backend-patterns | fastapi-patterns |
| frontend-guide | _none_ |
| language | python312 |
| conventions | conventions |

**Detection**: `pyproject.toml` contains `fastapi`, no `package.json`

## Profile: nextjs-only

| Category | Skill Name |
|----------|-----------|
| backend-patterns | _none_ |
| frontend-guide | nextjs16-guide |
| language | _none_ |
| conventions | conventions |

**Detection**: `package.json` contains `next`, no `pyproject.toml`

## Profile: django-react

| Category | Skill Name |
|----------|-----------|
| backend-patterns | django-patterns |
| frontend-guide | react-guide |
| language | python312 |
| conventions | conventions |

**Detection**: `requirements.txt` or `pyproject.toml` contains `django` AND `package.json` contains `react`

## Profile: express-vue

| Category | Skill Name |
|----------|-----------|
| backend-patterns | express-patterns |
| frontend-guide | vue-guide |
| language | _none_ |
| conventions | conventions |

**Detection**: `package.json` contains `express` AND `vue`

## Auto-Detection Priority

1. Check `pyproject.toml` → extract backend framework (fastapi, django, flask)
2. Check `requirements.txt` → fallback for Python projects
3. Check `package.json` → extract frontend framework (next, react, vue, angular)
4. Check `package.json` → extract backend framework (express, nestjs, hono)
5. If no match → ask human to choose or use generic profile

## Adding New Profiles

1. Create skill folder: `${CLAUDE_PLUGIN_ROOT}/skills/{framework}-patterns/SKILL.md`
2. Add a profile section in this file
3. Add detection rules
4. The profile will be available in `/start-project`

## Special Value: _none_

When a category is set to `_none_`, agents skip loading that skill entirely.
This is for projects that don't have a frontend (API-only) or don't need framework-specific backend patterns.
