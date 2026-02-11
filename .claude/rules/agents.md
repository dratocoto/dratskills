---
paths:
  - "plugins/ai-dev-team/agents/*.md"
  - "plugins/ai-dev-team/team.config.yaml"
---

# Quy ước Agent

## Định dạng file

Mỗi agent là một file `.md` trong `plugins/ai-dev-team/agents/` với YAML frontmatter:

```yaml
---
name: agent-name
description: >
  Khi nào và cách sử dụng agent này. Bao gồm 2-3 block <example>
  minh hoạ message từ user và hành vi mong đợi của agent.
---
```

## Các trường frontmatter bắt buộc

- `name` — phải khớp với key trong `team.config.yaml` dưới mục `agents:`
- `description` — giải thích khi nào Claude nên gọi agent này. Bao gồm các block `<example>`.

## Đồng bộ với team.config.yaml

team.config.yaml là **nguồn duy nhất** cho:
- `role` — tên vai trò dễ đọc
- `model` — model sử dụng (thường là `inherit`)
- `color` — màu hiển thị trên terminal
- `tools` — danh sách tool được phép (vd: `Read`, `Write`, `Bash(python3:*)`)
- `skills` — danh mục skill mà agent tải
- `context.max_files` — số file tối đa tải vào context

Khi chỉnh sửa agent, phải cập nhật CẢ file `.md` của agent VÀ `team.config.yaml`.

## Nội dung body Agent

Sau frontmatter, phần body `.md` là system prompt của agent. Nội dung nên:
- Tham chiếu `team.config.yaml` cho skill và giới hạn context
- Sử dụng `${CLAUDE_PLUGIN_ROOT}` cho đường dẫn đến tài nguyên plugin
- Bao gồm hướng dẫn từng bước cho quy trình làm việc của agent
- Tham chiếu đường dẫn `.ai-workspace/` cho các file workspace

## Danh sách Agent hiện tại (9)

| Agent | File | Vai trò |
|-------|------|---------|
| PM | pm-agent.md | Điều phối, phân công, quản lý STATE.md |
| BA | ba-agent.md | Làm rõ ý tưởng, viết requirement |
| Architect | architect-agent.md | Thiết kế hệ thống, tạo spec |
| Backend Dev | backend-dev-agent.md | Triển khai code backend |
| Frontend Dev | frontend-dev-agent.md | Triển khai code frontend |
| Reviewer | reviewer-agent.md | Review code |
| Tester | test-agent.md | Viết và chạy test |
| QA | qa-agent.md | Kiểm thử nghiệm thu, kiểm tra bảo mật |
| Researcher | researcher-agent.md | Nghiên cứu kỹ thuật, best practices |
