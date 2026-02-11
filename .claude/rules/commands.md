---
paths:
  - "plugins/ai-dev-team/commands/*.md"
---

# Quy ước Command

## Định dạng file

Mỗi command là một file `.md` trong `plugins/ai-dev-team/commands/`:

```yaml
---
description: Mô tả ngắn về chức năng của command
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(ls:*), Bash(tree:*)
argument-hint: <mô-tả-tham-số>
---
```

## Các trường frontmatter bắt buộc

- `description` — hiển thị trong danh sách command khi user gõ `/ai-dev-team:`
- `allowed-tools` — danh sách tool command được phép dùng (ranh giới bảo mật)
- `argument-hint` — (tuỳ chọn) gợi ý về tham số command

## Nội dung body Command

Sau frontmatter, body là hướng dẫn thực thi của command. Nội dung nên:
- Sử dụng `${CLAUDE_PLUGIN_ROOT}` để tham chiếu file plugin (skill, template, config)
- Tham chiếu `.ai-workspace/` cho các file workspace
- Bao gồm hướng dẫn từng bước rõ ràng
- Chỉ rõ vai trò agent nào tham gia

## Quy ước đặt tên

- Tên file trở thành tên command: `new-feature.md` → `/ai-dev-team:new-feature`
- Dùng kebab-case cho tên file
- Giữ tên ngắn gọn và hướng hành động

## Danh sách Command hiện tại (7)

| Command | File | Mô tả |
|---------|------|-------|
| start-project | start-project.md | Khởi tạo workspace, phát hiện stack |
| new-feature | new-feature.md | Bắt đầu feature (BA làm rõ → PM điều phối) |
| design | design.md | Tạo thiết kế kỹ thuật |
| implement | implement.md | Triển khai task card tiếp theo |
| test | test.md | Tạo test |
| review | review.md | Chạy review QA |
| status | status.md | Hiển thị trạng thái dự án |
