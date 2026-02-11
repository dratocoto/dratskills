---
paths:
  - "plugins/ai-dev-team/agents/*.md"
  - "plugins/ai-dev-team/team.config.yaml"
---

# Agent Conventions

## File Format

Each agent is a `.md` file in `plugins/ai-dev-team/agents/` with YAML frontmatter:

```yaml
---
name: agent-name
description: >
  When and how to use this agent. Include 2-3 <example> blocks
  showing user messages and expected agent behavior.
---
```

## Required Frontmatter Fields

- `name` — must match the key in `team.config.yaml` under `agents:`
- `description` — explains when Claude should invoke this agent. Include `<example>` blocks.

## Sync with team.config.yaml

team.config.yaml is the **single source of truth** for:
- `role` — human-readable role name
- `model` — which model to use (typically `inherit`)
- `color` — terminal output color
- `tools` — allowed tool list (e.g., `Read`, `Write`, `Bash(python3:*)`)
- `skills` — skill categories this agent loads
- `context.max_files` — max files to load into context

When modifying an agent, update BOTH the agent `.md` file AND `team.config.yaml`.

## Agent Body Content

After frontmatter, the `.md` body is the agent's system prompt. It should:
- Reference `team.config.yaml` for skills and context limits
- Use `${CLAUDE_PLUGIN_ROOT}` for file paths to plugin resources
- Include step-by-step instructions for the agent's workflow
- Reference `.ai-workspace/` paths for workspace files

## Current Agents (9)

| Agent | File | Role |
|-------|------|------|
| PM | pm-agent.md | Coordinator, delegates, manages STATE.md |
| BA | ba-agent.md | Clarifies ideas, writes requirements |
| Architect | architect-agent.md | Designs system, creates specs |
| Backend Dev | backend-dev-agent.md | Implements backend code |
| Frontend Dev | frontend-dev-agent.md | Implements frontend code |
| Reviewer | reviewer-agent.md | Peer code review |
| Tester | test-agent.md | Writes and runs tests |
| QA | qa-agent.md | Acceptance testing, security checks |
| Researcher | researcher-agent.md | Technical research, best practices |
