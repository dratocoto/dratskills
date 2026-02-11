---
description: Tạo thiết kế kỹ thuật cho feature hiện tại
allowed-tools: Read, Write, Glob, Grep, Bash(ls:*), Bash(tree:*)
argument-hint: [feature-id]
---

Kích hoạt giai đoạn thiết kế kỹ thuật cho một feature.

Đọc hướng dẫn workflow: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`
Đọc template thiết kế: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/design-spec-template.md`

1. Đọc `.ai-workspace/STATE.md` để xác định feature mục tiêu (hoặc dùng `$1` là FEAT-XXX)

2. Đọc `${CLAUDE_PLUGIN_ROOT}/team.config.yaml` → tìm `architect-agent` → lấy danh mục skill
   Đọc `.ai-workspace/stack.config.yaml` → ánh xạ danh mục sang tên skill cụ thể → tải từng skill

3. Đọc yêu cầu đã được phê duyệt: `.ai-workspace/features/FEAT-XXX/requirement.md`

4. Phân tích codebase hiện tại:
   - Chạy `tree -L 3 src/` để nắm cấu trúc
   - Đọc 2-3 file mẫu hiện có để tuân theo convention
   - Đọc `.ai-workspace/CONVENTIONS.md` để nắm quy tắc coding

5. Tạo `.ai-workspace/features/FEAT-XXX/design.md` bao gồm:
   - Tổng quan kiến trúc (sơ đồ Mermaid)
   - Mô hình dữ liệu (dạng bảng)
   - API endpoints (kèm schema request/response)
   - **Kế hoạch file** (mọi file cần tạo/sửa — dùng cho File Conflict Map)
   - Các task triển khai (có thứ tự, mỗi task tối đa 3 file, **gắn nhãn `backend` hoặc `frontend`**)
   - Quyết định thiết kế kèm lý do

6. PM cập nhật STATE.md:
   - Đặt phase FEAT-XXX thành DESIGN
   - Cập nhật **File Conflict Map** với các file từ kế hoạch file trong thiết kế
   - Kiểm tra xung đột với các feature song song khác

7. Trình bày thiết kế cho người dùng:
   - Hiển thị sơ đồ component
   - Hiển thị kế hoạch file
   - Hiển thị phân chia task (kèm nhãn backend/frontend)
   - Nếu phát hiện xung đột file → nêu bật và đề xuất giải pháp
   - Hỏi: "Phê duyệt thiết kế này? Phê duyệt / Chỉnh sửa / Từ chối"

Nếu được phê duyệt → tạo task cards trong `features/FEAT-XXX/tasks/` và chuyển sang phase IMPLEMENT.
