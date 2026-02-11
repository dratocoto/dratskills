# ai-dev-team — Claude Code Plugin

Plugin đội phát triển AI đa agent với 9 agent: PM, BA, Architect, Backend Dev, Frontend Dev, Reviewer, Tester, QA, Researcher.

## Kiến trúc

```
plugins/ai-dev-team/
├── .claude-plugin/plugin.json   # Plugin manifest (tên, phiên bản, mô tả)
├── agents/                      # Định nghĩa agent (.md với YAML frontmatter)
├── commands/                    # Slash commands (/start-project, /new-feature, v.v.)
├── skills/                      # Skill packs (SKILL.md + tuỳ chọn references/, templates/)
├── hooks/hooks.json             # Trình xử lý sự kiện session
└── team.config.yaml             # NGUỒN SỰ THẬT DUY NHẤT cho toàn bộ cấu hình agent

.ai-workspace/                   # Workspace theo dự án (tạo bởi /start-project)
├── STATE.md                     # Dashboard trạng thái dự án hiện tại
├── stack.config.yaml            # Ánh xạ skill categories → tên skill thực tế
├── CONVENTIONS.md               # Quy tắc coding chung
├── features/FEAT-XXX/           # Workspace theo phạm vi feature
├── discussions/                 # Thảo luận xuyên feature
└── decisions/                   # Architecture Decision Records
```

## Hệ thống cấu hình hai cấp

1. **team.config.yaml** (cấp plugin, tĩnh) — định nghĩa agents, skill _categories_ của từng agent, và thiết lập workflow
2. **stack.config.yaml** (theo dự án, được sinh bởi /start-project) — ánh xạ categories sang tên thư mục skill thực tế
   - Ví dụ: `backend-patterns` → `fastapi-patterns`, `language` → `python312`
   - Giá trị đặc biệt `_none_` → agent bỏ qua category đó

Các agent đọc team.config.yaml để tìm skill categories, sau đó phân giải sang skill thực tế thông qua stack.config.yaml.

## Phát triển

```bash
# Test plugin local (không cần cài qua marketplace)
claude --plugin-dir ./plugins/ai-dev-team

# Kiểm tra hooks JSON
python3 -m json.tool < plugins/ai-dev-team/hooks/hooks.json

# Kiểm tra plugin manifest
claude plugin validate ./plugins/ai-dev-team

# Kiểm tra marketplace manifest
claude plugin validate .
```

## Quy tắc quan trọng

- **team.config.yaml là nguồn sự thật duy nhất** cho cấu hình agent (roles, skills, tools, context limits)
- **Frontmatter của agent .md PHẢI khớp** với entry tương ứng trong team.config.yaml (name, description, model, allowed-tools)
- **Chỉ plugin.json nằm trong .claude-plugin/** — không bao giờ đặt agents/, commands/, skills/, hoặc hooks/ bên trong đó
- **${CLAUDE_PLUGIN_ROOT}** là đường dẫn runtime tới plugin đã cài; sử dụng trong commands và hooks để tham chiếu file plugin
- **Tên marketplace là `dratskills`** (định nghĩa trong .claude-plugin/marketplace.json)

## Quy ước thành phần

Quy tắc chi tiết cho từng loại thành phần nằm trong `.claude/rules/`:

- **Agents:** Xem `.claude/rules/agents.md` — định dạng frontmatter, đồng bộ team.config.yaml
- **Skills:** Xem `.claude/rules/skills.md` — định dạng SKILL.md, cấu trúc thư mục
- **Commands:** Xem `.claude/rules/commands.md` — định dạng command .md, allowed-tools
- **Cấu trúc plugin:** Xem `.claude/rules/plugin-structure.md` — tài liệu tham chiếu kiến trúc đầy đủ

## Lỗi thường gặp

- Đặt thư mục thành phần bên trong `.claude-plugin/` thay vì thư mục gốc plugin
- Sửa skills của agent trong file agent .md thay vì team.config.yaml
- Sử dụng đường dẫn cứng thay vì `${CLAUDE_PLUGIN_ROOT}`
- Quên cập nhật cả frontmatter agent LẪN team.config.yaml khi thay đổi cấu hình agent
