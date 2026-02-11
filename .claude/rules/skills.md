---
paths:
  - "plugins/ai-dev-team/skills/**/*"
  - "plugins/ai-dev-team/team.config.yaml"
---

# Quy ước Skill

## Cấu trúc thư mục

Mỗi skill là một thư mục trong `plugins/ai-dev-team/skills/` chứa tối thiểu một file `SKILL.md`:

```
skills/
├── skill-name/
│   ├── SKILL.md              # Tài liệu skill chính (bắt buộc)
│   ├── references/           # Tài liệu tham khảo bổ sung (tuỳ chọn)
│   │   └── topic.md
│   └── templates/            # Template cho agent sử dụng (tuỳ chọn)
│       └── template-name.md
```

## Định dạng SKILL.md

```yaml
---
name: skill-name
description: >
  Khi nào skill này nên được tải. Claude dùng thông tin này để quyết định mức độ liên quan.
  Bao gồm từ khoá kích hoạt (vd: "FastAPI", "Python 3.12", "conventions").
version: 0.1.0
---
```

Nội dung body là cơ sở kiến thức của skill — được tải vào context agent khi danh mục skill khớp.

## Phân giải Skill hai cấp

1. **team.config.yaml** định nghĩa _danh mục_ skill mà mỗi agent cần:
   ```yaml
   agents:
     backend-dev-agent:
       skills: [backend-patterns, language, conventions]
   ```

2. **stack.config.yaml** (theo dự án) ánh xạ danh mục sang tên thư mục skill thực tế:
   ```yaml
   skills:
     backend-patterns: fastapi-patterns
     language: python312
     conventions: conventions
     frontend-guide: _none_    # Agent bỏ qua danh mục này
   ```

## Thêm Skill mới

1. Tạo thư mục: `skills/new-skill-name/SKILL.md`
2. Thêm YAML frontmatter với `name`, `description`, `version`
3. Đăng ký danh mục skill trong `team.config.yaml` dưới `skill_categories:` (nếu là danh mục mới)
4. Ánh xạ danh mục trong template stack profile tại `skills/workflow-guide/references/stack-profiles.md`

## Danh sách Skill hiện tại

| Skill | Danh mục | Mục đích |
|-------|----------|----------|
| conventions | conventions | Chuẩn coding cho tất cả agent |
| fastapi-patterns | backend-patterns | Các pattern FastAPI |
| nextjs16-guide | frontend-guide | Các pattern Next.js 16 |
| python312 | language | Tính năng và idiom Python 3.12 |
| workflow-guide | (nội bộ) | Tài liệu workflow, template, tham khảo |
