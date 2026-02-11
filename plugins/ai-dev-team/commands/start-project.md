---
description: Khởi tạo workspace AI cho dự án
allowed-tools: Read, Write, Bash(mkdir:*), Bash(tree:*), Bash(ls:*), Bash(cat:*), Bash(grep:*)
argument-hint: [project-name]
---

Khởi tạo workspace AI Dev Team cho dự án hiện tại.

Đọc hướng dẫn workflow tại `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`.
Đọc cấu hình team tại `${CLAUDE_PLUGIN_ROOT}/team.config.yaml`.

## Bước 1: Tạo cấu trúc thư mục workspace

```
.ai-workspace/
├── STATE.md
├── stack.config.yaml     ← Ánh xạ skill (tự phát hiện, có thể ghi đè)
├── CONVENTIONS.md
├── CHECKLIST.md
├── features/             ← Mỗi feature có thư mục riêng
├── discussions/          ← Thảo luận xuyên feature
└── decisions/            ← Architecture Decision Records dùng chung
```

Lưu ý: Thư mục riêng cho từng feature (`features/FEAT-XXX/`) được tạo bởi lệnh `/new-feature`.

## Bước 2: Tự động phát hiện tech stack

Đọc `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/references/stack-profiles.md` để tham khảo định nghĩa profile.

Thực hiện các bước kiểm tra sau (theo thứ tự):

1. **Kiểm tra Python backend**:
   - Nếu `pyproject.toml` tồn tại → đọc file, tìm `fastapi`, `django`, `flask` trong dependencies
   - Hoặc nếu `requirements.txt` tồn tại → grep tên framework
   - Ghi nhận: backend framework + `python312` làm ngôn ngữ

2. **Kiểm tra Node.js frontend/backend**:
   - Nếu `package.json` tồn tại → đọc file, tìm `next`, `react`, `vue`, `angular`, `express`, `nestjs` trong dependencies
   - Ghi nhận: frontend/backend framework

3. **Khớp với profile**:
   - `fastapi` + `next` → profile `fastapi-nextjs`
   - `fastapi` only → profile `fastapi-only`
   - `next` only → profile `nextjs-only`
   - `django` + `react` → profile `django-react`
   - Không khớp → hỏi người dùng chọn

4. **Tạo `.ai-workspace/stack.config.yaml`** từ profile đã khớp:
   - Đọc template: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/stack-config-template.yaml`
   - Điền thông tin: stack đã phát hiện, ánh xạ skill từ profile
   - Hiển thị cho người dùng: "Đã phát hiện stack: [backend] + [frontend]. Skill đã được cấu hình."

## Bước 3: Khởi tạo các file dự án

1. Khởi tạo STATE.md từ template tại `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/state-template.md`
   - Đặt tên dự án là `$1` hoặc lấy từ package.json/pyproject.toml
   - Đặt stack dựa trên ngôn ngữ đã phát hiện

2. Khởi tạo CONVENTIONS.md:
   - Đọc stack.config.yaml để tìm skill `conventions`
   - Đọc `${CLAUDE_PLUGIN_ROOT}/skills/{conventions_skill}/SKILL.md`
   - Sao chép các phần convention phù hợp dựa trên stack đã phát hiện

3. Tạo CHECKLIST.md từ template tại `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/checklist-template.md`

## Bước 4: Báo cáo và chào mừng

1. Quét cấu trúc dự án hiện tại với `tree -L 3 src/` (hoặc `app/`, `pages/`) và báo cáo:
   - Stack đã phát hiện
   - Các module hiện có
   - Số lượng file hiện tại

2. Hiển thị ánh xạ skill trong stack.config.yaml:
   ```
   Stack đã cấu hình (stack.config.yaml):
     backend-patterns → fastapi-patterns
     frontend-guide   → nextjs16-guide
     language          → python312
     conventions       → conventions

   Skill được gán cho agent (team.config.yaml):
     Architect      → [backend-patterns, frontend-guide, conventions]
     Backend Dev    → [backend-patterns, language, conventions]
     Frontend Dev   → [frontend-guide, conventions]
     Reviewer       → [backend-patterns, frontend-guide, language, conventions]

   Để thay đổi: sửa .ai-workspace/stack.config.yaml (ánh xạ skill)
                 sửa team.config.yaml (gán skill cho agent)
   ```

3. Hiển thị thông điệp chào mừng kèm danh sách lệnh có sẵn và tổng quan workflow.

Nếu dự án chưa tồn tại, hỏi người dùng muốn sử dụng stack nào và đề xuất tạo khung dự án.

## Phát hiện lại Stack

Nếu dependencies của dự án thay đổi (ví dụ: chuyển từ React sang Next.js), người dùng có thể chạy lại `/start-project` trên workspace đã có. Lệnh sẽ:
1. Phát hiện lại stack từ các file dependency hiện tại
2. Cập nhật `.ai-workspace/stack.config.yaml` với ánh xạ skill mới
3. Giữ nguyên STATE.md, features/, và tất cả file workspace khác
4. Hiển thị diff của những thay đổi trong cấu hình skill
