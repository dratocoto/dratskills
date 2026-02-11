---
paths:
  - "plugins/ai-dev-team/commands/*.md"
---

# Command Conventions

## File Format

Each command is a `.md` file in `plugins/ai-dev-team/commands/`:

```yaml
---
description: Short description of what the command does
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(ls:*), Bash(tree:*)
argument-hint: <argument-description>
---
```

## Required Frontmatter Fields

- `description` — shown in command list when user types `/ai-dev-team:`
- `allowed-tools` — tools the command is allowed to use (security boundary)
- `argument-hint` — (optional) hint for command arguments

## Command Body

After frontmatter, the body is the command's execution instructions. It should:
- Use `${CLAUDE_PLUGIN_ROOT}` to reference plugin files (skills, templates, configs)
- Reference `.ai-workspace/` for workspace files
- Include clear step-by-step instructions
- Specify which agent roles are involved

## Naming Convention

- File name becomes the command name: `new-feature.md` → `/ai-dev-team:new-feature`
- Use kebab-case for file names
- Keep names short and action-oriented

## Current Commands (7)

| Command | File | Description |
|---------|------|-------------|
| start-project | start-project.md | Initialize workspace, detect stack |
| new-feature | new-feature.md | Start feature (BA clarification → PM coordination) |
| design | design.md | Create technical design |
| implement | implement.md | Implement next task card |
| test | test.md | Generate tests |
| review | review.md | Run QA review |
| status | status.md | Show project state |
