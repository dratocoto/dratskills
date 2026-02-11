---
description: Chạy review QA cho một feature
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(ruff:*), Bash(mypy:*), Bash(pytest:*), Bash(npx:*), Bash(tree:*)
argument-hint: [feature-id]
---

Chạy review QA toàn diện cho một feature (cổng kiểm tra chất lượng cuối cùng).

Đọc hướng dẫn workflow: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`

1. Đọc `.ai-workspace/STATE.md` để xác định feature mục tiêu (hoặc dùng `$1` là FEAT-XXX)

2. Đọc `${CLAUDE_PLUGIN_ROOT}/team.config.yaml` → tìm `qa-agent` → lấy danh mục skill
   Đọc `.ai-workspace/stack.config.yaml` → ánh xạ danh mục sang tên skill cụ thể → tải từng skill

3. Đọc `.ai-workspace/features/FEAT-XXX/handoffs/HANDOFF-latest.md` để lấy danh sách file
4. Đọc `.ai-workspace/features/FEAT-XXX/requirement.md` để lấy tiêu chí chấp nhận
5. Đọc `.ai-workspace/features/FEAT-XXX/design.md` để lấy kiến trúc mong đợi
6. Đọc `.ai-workspace/CONVENTIONS.md` để nắm quy tắc coding

7. Chạy kiểm tra tự động dựa trên stack.config.yaml:
   - Python: `ruff check src/`, `mypy src/`, `pytest --cov -q`
   - TypeScript: `npx tsc --noEmit`, `npx eslint src/`, `npx vitest --coverage`

8. Với mỗi file source trong feature, review:

   **Tiêu chí chấp nhận:**
   - Từng AC trong tài liệu yêu cầu → PASS/FAIL

   **Bảo mật:**
   - Có kiểm tra đầu vào
   - Không có secret được hardcode
   - Có xác thực trên các route được bảo vệ
   - Không để lộ stack traces trong response

   **Sẵn sàng production:**
   - Logging có cấu trúc (không dùng print/console.log)
   - Không có code debug hoặc code bị comment
   - Cấu hình được tách ra bên ngoài
   - Response lỗi nhất quán

9. Viết báo cáo QA: `.ai-workspace/features/FEAT-XXX/reviews/qa-report.md`
   Định dạng: Xem prompt của QA agent để lấy template

10. PM cập nhật `.ai-workspace/STATE.md` cho FEAT-XXX sang phase QA

11. Trình bày kết quả cho người dùng:
   - Phán quyết: APPROVED / NEEDS_CHANGES / REJECTED
   - Vấn đề nghiêm trọng (nếu có)
   - Số lượng cảnh báo
   - Phần trăm coverage
   - Hỏi: "Phê duyệt để merge? Hay cần sửa các vấn đề trước?"
