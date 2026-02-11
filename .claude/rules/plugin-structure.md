---
paths:
  - "plugins/ai-dev-team/**/*"
  - ".claude-plugin/*"
---

# Tham chiếu cấu trúc Plugin

## Bố cục Repository Marketplace

```
/ (gốc repo)
├── .claude-plugin/
│   └── marketplace.json        # Danh mục marketplace (name: "dratskills")
├── plugins/
│   └── ai-dev-team/            # Plugin
│       ├── .claude-plugin/
│       │   └── plugin.json     # Manifest plugin (name, version, description, author)
│       ├── agents/             # 9 định nghĩa agent (file .md)
│       │   ├── pm-agent.md
│       │   ├── ba-agent.md
│       │   ├── architect-agent.md
│       │   ├── backend-dev-agent.md
│       │   ├── frontend-dev-agent.md
│       │   ├── reviewer-agent.md
│       │   ├── test-agent.md
│       │   ├── qa-agent.md
│       │   └── researcher-agent.md
│       ├── commands/           # 7 slash command
│       │   ├── start-project.md
│       │   ├── new-feature.md
│       │   ├── design.md
│       │   ├── implement.md
│       │   ├── test.md
│       │   ├── review.md
│       │   └── status.md
│       ├── skills/             # Gói skill
│       │   ├── conventions/SKILL.md
│       │   ├── fastapi-patterns/SKILL.md
│       │   ├── nextjs16-guide/SKILL.md
│       │   ├── python312/SKILL.md
│       │   └── workflow-guide/
│       │       ├── SKILL.md
│       │       ├── references/
│       │       └── templates/
│       ├── hooks/
│       │   └── hooks.json      # Hook SessionStart + PreToolUse
│       └── team.config.yaml    # Cấu hình trung tâm (agent, skill, workflow)
├── CLAUDE.md                   # Tài liệu cho AI và contributor
├── README.md                   # Hướng dẫn cài đặt và sử dụng
└── .claude/rules/              # Quy tắc phát triển theo module
```

## Hành vi Runtime

Khi cài đặt qua `/plugin install`, Claude Code sao chép plugin vào `~/.claude/plugins/cache/`. Khi chạy:
- `${CLAUDE_PLUGIN_ROOT}` trỏ đến thư mục plugin đã cache
- Command và hook sử dụng biến này để tham chiếu file plugin
- Symlink trong source được theo dõi khi sao chép
- File ngoài thư mục plugin KHÔNG truy cập được (không dùng `../`)

## Workspace theo dự án (.ai-workspace/)

Được tạo bởi `/ai-dev-team:start-project`. Không thuộc plugin — nằm trong dự án của user:

```
.ai-workspace/
├── STATE.md              # Dashboard tổng quan cho tất cả feature
├── stack.config.yaml     # Ánh xạ danh mục skill → tên skill thực tế
├── CONVENTIONS.md        # Quy tắc coding dự án (tạo từ skill conventions)
├── CHECKLIST.md          # Checklist chất lượng trước commit
├── features/FEAT-XXX/    # Workspace theo feature
│   ├── requirement.md    # Requirement có cấu trúc của BA
│   ├── design.md         # Thiết kế kỹ thuật của Architect
│   ├── tasks/            # Thẻ task triển khai
│   ├── reviews/          # Review đồng nghiệp + báo cáo QA
│   ├── discussions/      # Thảo luận trong feature
│   └── handoffs/         # Tài liệu chuyển giao giữa các phase
├── discussions/          # Thảo luận xuyên feature
└── decisions/            # Bản ghi quyết định kiến trúc (ADR)
```
