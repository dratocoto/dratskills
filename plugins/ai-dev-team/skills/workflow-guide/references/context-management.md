# Chiến lược quản lý ngữ cảnh

## Vấn đề cốt lõi

Các AI agent có context window giới hạn. Một dự án thông thường có thể có hàng trăm file.
Tải mọi thứ = giảm sự tập trung = chất lượng code kém.

**Giải pháp**: Bộ nhớ chia sẻ dựa trên file + tải ngữ cảnh theo phạm vi task.

## Nguyên tắc: "Chỉ tải những gì cần thiết"

Mỗi lần tương tác của agent chỉ nên tải SỐ LƯỢNG TỐI THIỂU file cần thiết để hoàn thành task.

### Ngân sách ngữ cảnh cho mỗi Agent

| Agent        | Ngân sách | Chi tiết                                                                                         |
| ------------ | --------- | ------------------------------------------------------------------------------------------------ |
| PM           | ~4 file   | STATE.md + stack.config.yaml + handoff hiện tại + 1 thảo luận (nếu OPEN)                        |
| BA           | ~5 file   | STATE.md + stack.config.yaml + cấu trúc codebase (ls/tree) + yêu cầu hiện có (nếu đang chỉnh sửa) |
| Architect    | ~5 file   | tài liệu yêu cầu + kết quả tree + 2-3 pattern hiện có                                          |
| Backend Dev  | ~5 file   | task card + skill đã resolve + phần convention + 2-3 file mã nguồn                               |
| Frontend Dev | ~5 file   | task card + skill đã resolve + phần convention + 2-3 file mã nguồn                               |
| Reviewer     | ~5 file   | task card + skill đã resolve + CONVENTIONS.md + các file mã nguồn đang review                    |
| Test         | ~5 file   | phần spec + 2-3 file mã nguồn cần test + convention                                             |
| QA           | ~7 file   | convention + checklist + file mã nguồn + file test                                               |

### Cách giữ trong ngân sách

**1. Tham chiếu có cấu trúc, không đọc toàn bộ**

Thay vì tải toàn bộ CONVENTIONS.md, tham chiếu đến phần cụ thể:

```
Đọc: CONVENTIONS.md#python-models   ← Chỉ phần model
KHÔNG: CONVENTIONS.md               ← Toàn bộ file
```

Sử dụng header markdown làm anchor cho phần. Mỗi agent chỉ đọc phần liên quan.

**2. Task Card là nơi chứa ngữ cảnh**

Task card CHÍNH LÀ ngữ cảnh. Nó chứa:

- Việc cần làm (hướng dẫn)
- Cần đọc gì (tham chiếu file kèm lý do)
- Cần tạo gì (đầu ra mong đợi)
- Cách xác thực (tiêu chí chấp nhận)

Agent đọc task card → đọc các file được tham chiếu → làm việc → hoàn thành.

**3. Định dạng tài liệu gọn nhẹ**

CONVENTIONS.md sử dụng định dạng checklist, không phải văn xuôi:

```markdown
## Python Models

- [ ] Sử dụng SQLAlchemy declarative base
- [ ] Khóa chính UUID (import từ uuid)
- [ ] created_at, updated_at là các cột mặc định
- [ ] Type hint trên tất cả các cột
- [ ] Tên bảng = chữ thường số nhiều (users, products)
- [ ] Relationship lazy loading = "selectin"
```

Chỉ ~6 dòng thay vì 2 trang giải thích dài dòng. Cùng thông tin, ít token hơn 10 lần.

**4. Tiết lộ dần qua phân cấp file**

```
Cấp 0: CONVENTIONS.md         ← Luôn được tải (~200 dòng, gọn nhẹ)
Cấp 1: features/FEAT-XXX/design.md ← Tải cho feature cụ thể
Cấp 2: references/patterns.md ← Chỉ tải khi agent cần chi tiết sâu
```

Mỗi agent tải Cấp 0 + Cấp 1 liên quan. Cấp 2 chỉ khi cần.

## STATE.md là nguồn sự thật duy nhất

STATE.md là "bảng kanban" mà mọi agent đọc đầu tiên:

```markdown
# Trạng thái dự án

## Feature đang hoạt động: FEAT-003 Product Catalog

## Phase hiện tại: IMPLEMENTATION

## Task hiện tại: TASK-014 (3 trên 4)

## Tiến độ Feature

| Feature           | Yêu cầu | Thiết kế | Triển khai | Test | Review       |
| ----------------- | -------- | -------- | ---------- | ---- | ------------ |
| FEAT-001 Auth     | ✅       | ✅       | ✅         | ✅   | ✅ DONE      |
| FEAT-002 Users    | ✅       | ✅       | ✅         | ✅   | 🔄 IN REVIEW |
| FEAT-003 Products | ✅       | ✅       | 🔄 3/4     | ⏳   | ⏳           |
| FEAT-004 Orders   | ✅       | ⏳       | ⏳         | ⏳   | ⏳           |

## Hoạt động gần đây

- [2025-01-15 14:30] TASK-013 hoàn thành bởi backend-dev-agent
- [2025-01-15 14:00] TASK-012 hoàn thành bởi backend-dev-agent
- [2025-01-15 12:00] Thiết kế được phê duyệt cho FEAT-003
```

Quy tắc STATE.md:

- Tối đa 50 dòng (chỉ tóm tắt)
- Cập nhật sau mỗi task hoàn thành
- PM đọc khi bắt đầu mỗi lần tương tác
- Không bao giờ xóa, chỉ thêm/cập nhật

## Tránh Context Rot

**Context rot** = hiệu suất giảm khi cuộc hội thoại dài hơn.

Chiến lược phòng ngừa:

**1. Một Task mỗi lần gọi Agent**
Không yêu cầu Dev triển khai 5 task cùng lúc. Một task, một lần gọi.

**2. Bắt đầu mới cho mỗi Task**
Mỗi lần tương tác agent bắt đầu từ đầu:

- Đọc task card → Đọc file tham chiếu → Làm việc → Ghi đầu ra → Hoàn thành

**3. Tóm tắt, không tích lũy**
Sau khi hoàn thành một phase, viết bản tóm tắt (handoff) thay vì mang toàn bộ
lịch sử sang phase tiếp theo.

**4. Chia nhỏ Feature lớn**
Nếu một feature yêu cầu > 10 file, chia thành các sub-feature.
Mỗi sub-feature đi qua toàn bộ pipeline một cách độc lập.

## Giới hạn kích thước file

| Loại file       | Tối đa (dòng) | Nếu vượt quá                             |
| --------------- | -------------- | ----------------------------------------- |
| CONVENTIONS.md  | 200            | Chia thành các phần, dùng references/     |
| STATE.md        | 50             | Lưu trữ feature cũ vào history/          |
| Task card       | 40             | Chia thành task nhỏ hơn                   |
| Tài liệu yêu cầu | 60          | Dùng bullet point, không viết văn xuôi    |
| Tài liệu thiết kế | 100         | Chuyển chi tiết vào references/           |
| Handoff         | 30             | Viết ngắn gọn, tham chiếu file           |

## Những anti-pattern cần tránh

1. **Tải toàn bộ codebase** — Không bao giờ. Dùng tree + đọc có mục tiêu.
2. **Tài liệu nặng văn xuôi** — Dùng bảng, checklist, bullet point.
3. **Mang theo lịch sử hội thoại** — Mỗi agent bắt đầu mới với ngữ cảnh từ file.
4. **Một task card khổng lồ** — Chia thành các task tối đa 3 file.
5. **Trùng lặp thông tin** — Tham chiếu file, không sao chép nội dung.
6. **Bỏ qua cập nhật STATE.md** — Team mất khả năng phối hợp.
