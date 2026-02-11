---
description: Bắt đầu feature mới (kích hoạt BA làm rõ yêu cầu → PM điều phối)
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(ls:*), Bash(tree:*), Bash(mkdir:*)
argument-hint: <feature-description>
---

Bắt đầu giai đoạn làm rõ ý tưởng và phân tích yêu cầu cho một feature mới.

Đọc hướng dẫn workflow: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/SKILL.md`
Đọc template yêu cầu: `${CLAUDE_PLUGIN_ROOT}/skills/workflow-guide/templates/requirement-template.md`

## Bước 1: PM khởi tạo

1. Đọc `.ai-workspace/STATE.md` để lấy số FEAT-XXX tiếp theo
2. Tạo cấu trúc thư mục cho feature:
   ```
   .ai-workspace/features/FEAT-XXX/
   ├── tasks/
   ├── reviews/
   ├── discussions/
   └── handoffs/
   ```
3. Ghi nhận feature request vào bảng Parallel Features trong STATE.md với trạng thái: CLARIFY
4. Ủy quyền cho BA Agent trong phạm vi `features/FEAT-XXX/`

## Bước 2: BA thực hiện Phase 0 — Làm rõ ý tưởng (BẮT BUỘC)

1. Quét cấu trúc codebase hiện tại: `tree -L 2 src/` (nếu có) để nắm trạng thái hiện tại

2. Phân tích mô tả feature từ người dùng: `$ARGUMENTS`
   - Xác định những gì ĐÃ RÕ RÀNG trong mô tả
   - Xác định những gì MƠ HỒ hoặc KHÔNG RÕ RÀNG
   - Xác định những gì THIẾU (chưa được đề cập)

3. Tạo **3-7 câu hỏi làm rõ** sử dụng QA Question Framework của BA:
   - ĐỐI TƯỢNG: Ai sử dụng tính năng này? Vai trò/quyền hạn gì?
   - HÀNH VI: Hành vi chính, dữ liệu liên quan, luồng thành công/lỗi?
   - PHẠM VI: Giới hạn? Ranh giới? Trường hợp biên?
   - TÍCH HỢP: Kết nối với feature hiện có? API bên ngoài?
   - UX/NGHIỆP VỤ: Giao diện mong muốn? Sắp xếp? Phân tích dữ liệu?

   Quy tắc:
   - Ưu tiên câu hỏi có tác động lớn nhất lên đầu
   - Cung cấp gợi ý lựa chọn khi có thể (ví dụ: "Xác thực: JWT hay session-based?")
   - Đánh dấu câu hỏi không bắt buộc: "Bổ sung thêm (không bắt buộc):"
   - Nếu feature đã rất rõ ràng → chỉ hỏi 2-3 câu thiết yếu
   - KHÔNG hỏi quá nhiều (10+ câu hỏi)

4. Trình bày câu hỏi cho người dùng:
   - "Trước khi viết yêu cầu, tôi có một vài câu hỏi để đảm bảo nắm đúng ý tưởng của bạn:"
   - [Danh sách câu hỏi đánh số]
   - Chờ người dùng trả lời

5. Nếu câu trả lời phát sinh thêm điểm chưa rõ → hỏi thêm 1 vòng bổ sung (tối đa 3 câu hỏi)

## Bước 3: BA thực hiện Phase 1 — Viết yêu cầu

6. Sau khi làm rõ, tạo `.ai-workspace/features/FEAT-XXX/requirement.md` bao gồm:
   - Mô tả vấn đề (dựa trên kết quả làm rõ)
   - User stories được rút ra từ mô tả VÀ câu trả lời khi làm rõ
   - Tiêu chí chấp nhận (cụ thể, kiểm tra được — bao gồm các trường hợp biên đã thảo luận)
   - Ước lượng phạm vi (S/M/L/XL dựa trên độ phức tạp)
   - Ràng buộc kỹ thuật (dựa trên codebase hiện tại)
   - Phụ thuộc vào các feature hiện có
   - **Nhật ký làm rõ** — bảng Q&A đầy đủ để truy vết
   - **Quyết định chính** — các quyết định được đưa ra trong quá trình làm rõ
   - **Giả định** — những điểm vẫn chưa rõ

## Bước 4: PM trình duyệt

7. PM cập nhật `.ai-workspace/STATE.md`:
   - Đặt trạng thái feature thành REQ (giai đoạn yêu cầu)
   - Đặt feature hiện tại

8. PM trình bày yêu cầu cho người dùng:
   - Hiển thị yêu cầu có cấu trúc
   - Nêu bật các quyết định chính từ quá trình làm rõ
   - Hỏi: "Yêu cầu này đã nắm đúng ý bạn chưa? Phê duyệt / Chỉnh sửa / Từ chối"
   - Chờ người dùng phản hồi

Nếu được phê duyệt → PM viết handoff vào `features/FEAT-XXX/handoffs/` cho architect, cập nhật STATE.md sang phase DESIGN.
Nếu bị từ chối → PM gửi lại cho BA kèm phản hồi để chỉnh sửa (BA viết lại `features/FEAT-XXX/requirement.md`).
