# AI Dev Team — Claude Code Plugin Marketplace

A plugin marketplace for Claude Code providing a multi-agent AI development team.

## Plugins

### ai-dev-team

Multi-agent AI dev team (PM, BA, Architect, Backend Dev, Frontend Dev, Reviewer, Tester, QA, Researcher) with dynamic tech stack detection, parallel features, inter-agent discussions, peer code review, file conflict detection, and human oversight.

## Installation

```bash
# Register this marketplace
/plugin marketplace add https://github.com/dratocoto/ai-dev-team.git

# Install the plugin
/plugin install ai-dev-team
```

## Structure

```
plugins/
  ai-dev-team/
    .claude-plugin/plugin.json
    agents/          # Agent definitions (PM, BA, Architect, Dev, QA, etc.)
    commands/        # Slash commands (/start-project, /new-feature, etc.)
    skills/          # Skill packs (workflow-guide, conventions, etc.)
    hooks/           # Session hooks
    team.config.yaml # Central agent configuration
```
