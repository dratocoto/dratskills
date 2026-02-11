---
description: Triển khai task card tiếp theo
allowed-tools: Read, Write, Edit, Grep, Glob, Bash(python3:*), Bash(ruff:*), Bash(mypy:*), Bash(npm:*), Bash(npx:*), Bash(uv:*), Bash(node:*), Bash(ls:*), Bash(tree:*)
argument-hint: [task-id]
---

Triển khai một task cụ thể từ feature hiện tại.

Đọc hướng dẫn workflow: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`

1. Đọc `.ai-workspace/STATE.md` để tìm feature mục tiêu và task hiện tại (hoặc dùng `$1` là FEAT-XXX/TASK-XXX)

2. Đọc `${CLAUDE_PLUGIN_ROOT}/team.config.yaml` và `.ai-workspace/stack.config.yaml` để xác định tech stack

3. Đọc task card: `.ai-workspace/features/FEAT-XXX/tasks/TASK-XXX.md`

4. Kiểm tra nhãn task:
   - `backend` → chuyển cho **Backend Dev** agent
   - `frontend` → chuyển cho **Frontend Dev** agent
   - `full-stack` → Backend Dev trước, sau đó Frontend Dev

5. Dev agent sẽ:
   a. Tải skill từ team.config.yaml + stack.config.yaml
   b. CHỈ đọc các file được liệt kê trong phần "Files to Read" của task card
   c. Viết code tuân theo convention và pattern của framework
   d. Tự kiểm tra (linter, type-check)
   e. Cập nhật trạng thái task card thành DONE
   f. Viết `.ai-workspace/features/FEAT-XXX/handoffs/HANDOFF-latest.md`

6. PM cập nhật `.ai-workspace/STATE.md` với tiến độ của FEAT-XXX

7. Kích hoạt **Reviewer** agent để **review code ngang hàng** (Phase 4 — review từng task, không phải QA)
   - Reviewer viết `features/FEAT-XXX/reviews/TASK-XXX-review.md`
   - Nếu CHANGES_REQUESTED → Dev sửa → Reviewer review lại (tối đa 3 vòng)

8. Nếu Reviewer phê duyệt và còn task tiếp theo → hiển thị tiến độ và hỏi người dùng có muốn tiếp tục không
   Nếu tất cả task hoàn thành → đề xuất chuyển sang giai đoạn test (`/test`)
   Lưu ý: Review QA toàn diện là giai đoạn riêng — sử dụng `/review` sau khi test pass
