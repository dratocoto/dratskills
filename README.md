# AI Dev Team — Claude Code Plugin

A multi-agent AI development team for [Claude Code](https://claude.com/claude-code). Nine specialized agents collaborate through a structured workflow to build software — from requirements to QA — with human oversight at every checkpoint.

**Agents:** PM, Business Analyst, Architect, Backend Dev, Frontend Dev, Reviewer, Tester, QA, Researcher

**Key features:**
- Dynamic tech stack detection (FastAPI, Next.js, Django, etc.)
- Parallel feature development with file conflict detection
- Inter-agent discussions and peer code review
- Human-in-the-loop checkpoints at every phase

## Prerequisites

- [Claude Code](https://claude.com/claude-code) version **1.0.33** or later
- Run `claude --version` to check

## Installation

### Option A: Interactive UI

1. Open Claude Code in your terminal
2. Type `/plugin` to open the plugin manager
3. Go to the **Marketplaces** tab (press Tab to cycle)
4. Add this marketplace: `dratocoto/ai-dev-team`
5. Go to the **Discover** tab
6. Find **ai-dev-team** and press Enter to install
7. Choose your installation scope (User / Project / Local)

### Option B: CLI Commands

```bash
# Step 1: Add the marketplace
/plugin marketplace add dratocoto/ai-dev-team

# Step 2: Install the plugin
/plugin install ai-dev-team@dratskills
```

To install at project scope (shared with collaborators):

```bash
/plugin install ai-dev-team@dratskills --scope project
```

### Verify Installation

After installation, you should see the plugin commands available:

```bash
/ai-dev-team:status
```

### Local Development

To test the plugin without installing via marketplace:

```bash
claude --plugin-dir ./plugins/ai-dev-team
```

## Quick Start

```bash
# 1. Initialize the AI workspace in your project
/ai-dev-team:start-project my-app

# 2. Start a new feature
/ai-dev-team:new-feature Add user authentication with JWT

# 3. Follow the workflow: BA clarifies → you approve → Architect designs → you approve → Dev implements
```

The team follows a 7-phase workflow: **Clarify → Requirement → Design → Implement → Review → Test → QA**. You approve at each checkpoint.

## Commands

| Command | Description |
|---------|-------------|
| `/ai-dev-team:start-project` | Initialize AI workspace, detect tech stack, configure skills |
| `/ai-dev-team:new-feature` | Start a new feature (BA asks clarifying questions first) |
| `/ai-dev-team:design` | Create technical design for the current feature |
| `/ai-dev-team:implement` | Implement the next task card |
| `/ai-dev-team:test` | Generate tests for implemented code |
| `/ai-dev-team:review` | Run QA review on a feature |
| `/ai-dev-team:status` | Show project state and all parallel features |

## Plugin Structure

```
plugins/ai-dev-team/
├── .claude-plugin/plugin.json   # Plugin manifest
├── agents/                      # 9 agent definitions (.md)
├── commands/                    # 7 slash commands
├── skills/                      # Skill packs (workflow-guide, conventions, etc.)
├── hooks/                       # Session event handlers (hooks.json)
└── team.config.yaml             # Central agent configuration
```

## Marketplace Info

- **Marketplace name:** `dratskills`
- **Plugin name:** `ai-dev-team`
- **Version:** 0.5.0

## Managing the Plugin

```bash
# Update to latest version
/plugin update ai-dev-team@dratskills

# Disable temporarily
/plugin disable ai-dev-team@dratskills

# Re-enable
/plugin enable ai-dev-team@dratskills

# Uninstall
/plugin uninstall ai-dev-team@dratskills
```

## License

MIT
