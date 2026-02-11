---
paths:
  - "plugins/ai-dev-team/skills/**/*"
  - "plugins/ai-dev-team/team.config.yaml"
---

# Skill Conventions

## Directory Structure

Each skill is a directory under `plugins/ai-dev-team/skills/` containing at minimum a `SKILL.md`:

```
skills/
├── skill-name/
│   ├── SKILL.md              # Main skill document (required)
│   ├── references/           # Supporting reference docs (optional)
│   │   └── topic.md
│   └── templates/            # File templates for agents to use (optional)
│       └── template-name.md
```

## SKILL.md Format

```yaml
---
name: skill-name
description: >
  When this skill should be loaded. Used by Claude to decide relevance.
  Include trigger keywords (e.g., "FastAPI", "Python 3.12", "conventions").
version: 0.1.0
---
```

Body content is the skill's knowledge base — loaded into agent context when the skill category matches.

## Two-Level Skill Resolution

1. **team.config.yaml** defines which skill _categories_ each agent needs:
   ```yaml
   agents:
     backend-dev-agent:
       skills: [backend-patterns, language, conventions]
   ```

2. **stack.config.yaml** (per-project) maps categories to actual skill folder names:
   ```yaml
   skills:
     backend-patterns: fastapi-patterns
     language: python312
     conventions: conventions
     frontend-guide: _none_    # Agent skips this category
   ```

## Adding a New Skill

1. Create directory: `skills/new-skill-name/SKILL.md`
2. Add YAML frontmatter with `name`, `description`, `version`
3. Register the skill category in `team.config.yaml` under `skill_categories:` (if new category)
4. Map the category in stack profile templates at `skills/workflow-guide/references/stack-profiles.md`

## Current Skills

| Skill | Category | Purpose |
|-------|----------|---------|
| conventions | conventions | Coding standards for all agents |
| fastapi-patterns | backend-patterns | FastAPI framework patterns |
| nextjs16-guide | frontend-guide | Next.js 16 patterns |
| python312 | language | Python 3.12 features and idioms |
| workflow-guide | (internal) | Team workflow docs, templates, references |
