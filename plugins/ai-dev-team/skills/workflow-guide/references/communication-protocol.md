# Giao thức giao tiếp giữa các Agent

Cách các agent trao đổi với nhau — mô phỏng một đội phát triển thực tế.

## 3 Kênh giao tiếp

### Kênh 1: Handoff (Một chiều, có cấu trúc)

**Khi nào**: Agent A hoàn thành một phase, Agent B tiếp quản.
**Định dạng**: `features/FEAT-XXX/handoffs/HANDOFF-latest.md`
**Hướng đi**: Luôn A → PM → B (PM điều phối, giới hạn trong phạm vi feature)

```
BA hoàn thành yêu cầu → HANDOFF → PM đọc → kích hoạt Architect
Architect hoàn thành thiết kế → HANDOFF → PM đọc → tạo task card (gán nhãn backend/frontend)
Backend Dev hoàn thành task → HANDOFF → PM đọc → kích hoạt Reviewer
Frontend Dev hoàn thành task → HANDOFF → PM đọc → kích hoạt Reviewer
Reviewer phê duyệt → HANDOFF → PM đọc → kích hoạt task tiếp theo hoặc Tester
Tester hoàn thành → HANDOFF → PM đọc → kích hoạt QA
```

Đây là **luồng pipeline** — tuần tự và có cấu trúc.

### Kênh 2: Thảo luận (Đa chiều, hội thoại)

**Khi nào**: Một agent có câu hỏi, lo ngại, hoặc cần ý kiến từ agent khác.
**Định dạng**: Trong feature → `features/FEAT-XXX/discussions/DISC-XXX.md` | Xuyên feature → `discussions/DISC-CROSS-XXX.md`
**Hướng đi**: Bất kỳ agent → Bất kỳ agent (PM điều phối)

Thảo luận mô phỏng các cuộc trao đổi thực tế trong team:
- Reviewer hỏi Backend Dev về lựa chọn thiết kế
- Reviewer hỏi Frontend Dev về pattern UI
- Backend Dev hỏi Architect về tính khả thi của thiết kế
- Frontend Dev hỏi Backend Dev về API contract
- Tester hỏi BA để làm rõ tiêu chí chấp nhận
- Backend Dev ghi nhận lo ngại gửi Architect về thiết kế
- Frontend Dev hỏi Architect về ranh giới component
- QA hỏi mọi người về vấn đề xuyên suốt
- Bất kỳ agent nào yêu cầu Researcher điều tra kỹ thuật

**Luồng thảo luận:**
```
1. Agent A viết DISC-XXX.md với câu hỏi + ngữ cảnh
2. Agent A đánh dấu: Status: OPEN, Participants: [ai cần trả lời]
3. PM thấy thảo luận mở → chuyển đến người tham gia
4. Người tham gia đọc thảo luận → viết phản hồi
5. Nếu đã giải quyết → Status: RESOLVED + tóm tắt quyết định
6. Nếu cần thêm ý kiến → thêm vòng (tối đa 3 vòng)
7. PM cập nhật tài liệu liên quan với quyết định
```

**Quy tắc thảo luận:**
- Mỗi mục có header: `### [Tên Agent] → [Agent đích]`
- Viết ngắn gọn — vấn đề + các lựa chọn + đề xuất
- Tối đa 3 vòng mỗi thảo luận — sau đó PM chuyển lên người dùng
- Quyết định từ thảo luận được ghi vào `decisions/ADR-XXX.md`
- Bất kỳ agent nào cũng có thể mở thảo luận, không chỉ PM

### Kênh 3: Nhận xét Review (Phản hồi về sản phẩm công việc)

**Khi nào**: Reviewer hoặc QA phát hiện vấn đề trong code/test/thiết kế.
**Định dạng**: `features/FEAT-XXX/reviews/TASK-XXX-review.md` hoặc `features/FEAT-XXX/reviews/qa-report.md`
**Hướng đi**: Reviewer → Backend Dev hoặc Frontend Dev (qua PM), QA → bất kỳ agent (qua PM)

Nhận xét review mô phỏng các comment trên PR:
- Gắn nhãn theo mức độ: `[CRITICAL]`, `[WARNING]`, `[SUGGESTION]`, `[QUESTION]`
- Kèm đường dẫn file + số dòng khi có thể
- Kèm "code hiện tại" và "gợi ý sửa"
- Comment `[QUESTION]` tự động mở luồng thảo luận

**Chu trình Review:**
```
1. Reviewer viết review kèm nhận xét
2. Nếu APPROVED → phase tiếp theo
3. Nếu CHANGES_REQUESTED:
   a. PM gửi review cho Dev phù hợp (Backend Dev hoặc Frontend Dev dựa trên label của task)
   b. Dev đọc nhận xét, sửa code
   c. Dev viết phản hồi: "Đã sửa" / "Không sửa vì..." / "Cần thảo luận"
   d. Reviewer review lại CHỈ phần đã thay đổi
   e. Tối đa 3 vòng → sau đó PM chuyển lên người dùng
```

---

## Các tình huống thảo luận phổ biến

### Tình huống 1: Tính khả thi thiết kế (Backend Dev ↔ Architect)
```
Architect tạo thiết kế → Backend Dev đọc → phát hiện vấn đề
Backend Dev mở DISC: "Thiết kế yêu cầu dùng WebSocket, nhưng hạ tầng chưa hỗ trợ"
Architect trả lời: "Đúng rồi. Dùng SSE thay thế"
Quyết định → ADR-XXX: "Dùng SSE thay vì WebSocket do hạn chế hạ tầng"
```

### Tình huống 2: Tiêu chí chấp nhận mơ hồ (Tester ↔ BA)
```
Tester đọc spec → không rõ "đơn hàng gần đây" nghĩa là gì
Tester mở DISC: "'Gần đây' có nghĩa là 7 ngày, 30 ngày, hay có thể cấu hình?"
BA trả lời: "30 ngày gần nhất, không cấu hình được ở thời điểm hiện tại. Đã bổ sung vào yêu cầu."
Quyết định → cập nhật tài liệu yêu cầu
```

### Tình huống 3: Tranh luận Code Review (Reviewer ↔ Backend Dev ↔ Architect)
```
Reviewer nhận xét: "Nên dùng generator thay vì tải tất cả vào bộ nhớ"
Backend Dev trả lời: "Generator không hoạt động vì downstream cần random access"
Reviewer: "Vậy phân trang bằng cursor. Hỏi Architect nhé."
Architect: "Cursor pagination. Tối đa 100 mỗi trang."
Quyết định → cập nhật triển khai
```

### Tình huống 4: Vấn đề xuyên suốt (QA ↔ Tất cả)
```
QA phát hiện: "3 service đang kiểm tra auth theo cách khác nhau"
QA mở DISC với tất cả: "Cần một pattern auth thống nhất"
Architect: "Tạo middleware dùng chung. Đây là pattern..."
Quyết định → ADR + task card mới cho việc chuẩn hóa
```

### Tình huống 5: API Contract (Frontend Dev ↔ Backend Dev)
```
Frontend Dev đọc API spec → cần một trường không có trong response
Frontend Dev mở DISC: "Tôi cần `user.avatar_url` trong response của endpoint /me"
Backend Dev trả lời: "Có thể thêm. Sẽ đưa vào UserProfile schema."
Quyết định → cập nhật design.md, tạo task card mới nếu cần
```

### Tình huống 6: Nghiên cứu kỹ thuật (Bất kỳ Agent → Researcher qua PM)
```
Architect không chắc về chiến lược caching → yêu cầu PM nghiên cứu
PM kích hoạt Researcher: "So sánh Redis và in-memory caching cho session store"
Researcher điều tra → viết RESEARCH-001.md với bảng so sánh + đề xuất
PM chuyển kết quả về Architect
Architect tích hợp đề xuất vào thiết kế
```

---

## Cấu trúc file Thảo luận

```
.ai-workspace/features/FEAT-XXX/discussions/
├── DISC-001.md        ← Thảo luận tính khả thi thiết kế
├── DISC-002.md        ← Làm rõ tiêu chí chấp nhận
├── DISC-003.md        ← Thảo luận API contract
├── RESEARCH-001.md    ← Báo cáo kỹ thuật của Researcher
└── RESEARCH-002.md    ← Báo cáo nghiên cứu khác

.ai-workspace/discussions/
├── DISC-CROSS-001.md  ← Giải quyết xung đột file xuyên feature
└── DISC-CROSS-002.md  ← Pattern auth xuyên suốt
```

## Khi nào mở Thảo luận và khi nào tự xử lý

**Mở thảo luận khi:**
- Không chắc chắn về cách tiếp cận đúng
- Vấn đề ảnh hưởng nhiều hơn task hiện tại
- Không đồng ý với lựa chọn thiết kế hoặc triển khai
- Cần thông tin từ lĩnh vực của agent khác
- Quyết định có thể ảnh hưởng đến kiến trúc
- Cần Researcher điều tra vấn đề (yêu cầu qua PM)

**Tự xử lý khi:**
- Lỗi đơn giản với giải pháp rõ ràng
- Lỗi chính tả hoặc không nhất quán trong đặt tên
- Tài liệu convention đã có câu trả lời
- Nằm trong lĩnh vực của bạn và không ảnh hưởng người khác

---

## Vai trò của PM trong giao tiếp

PM là **người điều phối**, không phải nút thắt cổ chai:

1. **Điều phối**: PM đọc các thảo luận mở, kích hoạt agent phù hợp phản hồi
2. **Chuyển cấp**: Nếu thảo luận vượt quá 3 vòng → PM chuyển lên người dùng
3. **Ghi nhận**: PM đảm bảo quyết định được ghi lại trong ADR hoặc cập nhật yêu cầu
4. **Tóm tắt**: PM cung cấp cho người dùng bản tóm tắt thảo luận tại các checkpoint
5. **Giải tỏa**: Nếu các agent bất đồng → PM trình các lựa chọn cho người dùng quyết định
6. **Nghiên cứu**: PM kích hoạt Researcher khi bất kỳ agent nào yêu cầu điều tra kỹ thuật

PM KHÔNG:
- Trả lời câu hỏi kỹ thuật thay cho các agent
- Ghi đè đề xuất của agent
- Đóng thảo luận khi chưa có giải pháp
