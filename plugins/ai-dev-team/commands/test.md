---
description: Tạo test cho code đã triển khai
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(pytest:*), Bash(python3:*), Bash(npm:*), Bash(npx:*), Bash(ls:*), Bash(tree:*)
argument-hint: [feature-id]
---

Tạo bộ test toàn diện cho phần triển khai của một feature.

Đọc hướng dẫn workflow: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`

1. Đọc `.ai-workspace/STATE.md` để xác định feature mục tiêu (hoặc dùng `$1` là FEAT-XXX)

2. Đọc `${CLAUDE_PLUGIN_ROOT}/team.config.yaml` → tìm `test-agent` → lấy danh mục skill
   Đọc `.ai-workspace/stack.config.yaml` → ánh xạ danh mục sang tên skill cụ thể → tải từng skill

3. Đọc `.ai-workspace/features/FEAT-XXX/handoffs/HANDOFF-latest.md` để lấy danh sách file đã triển khai

4. Đọc thiết kế để nắm hành vi mong đợi: `.ai-workspace/features/FEAT-XXX/design.md`

5. Với mỗi file source đã triển khai:
   a. Đọc file source để hiểu methods, types, trường hợp lỗi
   b. Tạo file test bao gồm:
   - Test luồng chính (đầu vào hợp lệ → kết quả mong đợi)
   - Test trường hợp biên (giá trị biên, đầu vào rỗng)
   - Test trường hợp lỗi (đầu vào không hợp lệ → lỗi mong đợi)
   - Test kiểm tra schema
     c. Tuân theo pattern Arrange-Act-Assert
     d. Đặt tên test mô tả rõ ràng: `test_<unit>_<scenario>_<expected>`

6. Tạo shared fixtures nếu chưa có

7. Chạy test bằng runner phù hợp từ stack.config.yaml:
   - Python: `pytest tests/ -v --tb=short`
   - TypeScript: `npx vitest --run`

8. Chạy coverage:
   - Python: `pytest tests/ --cov=src --cov-report=term-missing -q`
   - TypeScript: `npx vitest --coverage`

9. Viết handoff vào `.ai-workspace/features/FEAT-XXX/handoffs/HANDOFF-latest.md`:
   - Kết quả test (pass/fail)
   - Coverage theo từng module
   - Test thất bại kèm chi tiết (nếu có)
   - Đề xuất: tiến hành QA hay sửa lỗi trước

10. PM cập nhật `.ai-workspace/STATE.md` cho FEAT-XXX sang phase TEST

Nếu tất cả test pass và coverage >= 80% → đề xuất chuyển sang giai đoạn QA.
Nếu test thất bại → báo cáo lỗi và đề xuất cách sửa.
