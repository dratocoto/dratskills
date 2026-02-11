# Plugin Structure Reference

## Marketplace Repository Layout

```
/ (repo root)
├── .claude-plugin/
│   └── marketplace.json        # Marketplace catalog (name: "dratskills")
├── plugins/
│   └── ai-dev-team/            # The plugin
│       ├── .claude-plugin/
│       │   └── plugin.json     # Plugin manifest (name, version, description, author)
│       ├── agents/             # 9 agent definitions (.md files)
│       │   ├── pm-agent.md
│       │   ├── ba-agent.md
│       │   ├── architect-agent.md
│       │   ├── backend-dev-agent.md
│       │   ├── frontend-dev-agent.md
│       │   ├── reviewer-agent.md
│       │   ├── test-agent.md
│       │   ├── qa-agent.md
│       │   └── researcher-agent.md
│       ├── commands/           # 7 slash commands
│       │   ├── start-project.md
│       │   ├── new-feature.md
│       │   ├── design.md
│       │   ├── implement.md
│       │   ├── test.md
│       │   ├── review.md
│       │   └── status.md
│       ├── skills/             # Skill packs
│       │   ├── conventions/SKILL.md
│       │   ├── fastapi-patterns/SKILL.md
│       │   ├── nextjs16-guide/SKILL.md
│       │   ├── python312/SKILL.md
│       │   └── workflow-guide/
│       │       ├── SKILL.md
│       │       ├── references/
│       │       └── templates/
│       ├── hooks/
│       │   └── hooks.json      # SessionStart + PreToolUse hooks
│       └── team.config.yaml    # Central config (agents, skills, workflow)
├── CLAUDE.md                   # Developer docs for AI and contributors
├── README.md                   # End-user installation and usage guide
└── .claude/rules/              # Modular development rules
```

## Runtime Behavior

When installed via `/plugin install`, Claude Code copies the plugin to `~/.claude/plugins/cache/`. At runtime:
- `${CLAUDE_PLUGIN_ROOT}` resolves to the cached plugin directory
- Commands and hooks use this variable to reference plugin files
- Symlinks in the source are followed during copying
- Files outside the plugin directory are NOT accessible (no `../` references)

## Per-Project Workspace (.ai-workspace/)

Created by `/ai-dev-team:start-project`. Not part of the plugin — lives in the user's project:

```
.ai-workspace/
├── STATE.md              # Global dashboard for all features
├── stack.config.yaml     # Maps skill categories → actual skill names
├── CONVENTIONS.md        # Project coding rules (generated from conventions skill)
├── CHECKLIST.md          # Pre-commit quality checklist
├── features/FEAT-XXX/    # Feature-scoped workspace
│   ├── requirement.md    # BA's structured requirement
│   ├── design.md         # Architect's technical design
│   ├── tasks/            # Implementation task cards
│   ├── reviews/          # Peer reviews + QA report
│   ├── discussions/      # Feature-scoped discussions
│   └── handoffs/         # Phase transition documents
├── discussions/          # Cross-feature discussions
└── decisions/            # Architecture Decision Records
```
