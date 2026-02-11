# Giao thức Handoff

## Tại sao Handoff quan trọng

Các AI agent có context window giới hạn. Khi một agent kết thúc và agent khác tiếp quản,
agent mới bắt đầu với context HOÀN TOÀN MỚI. File handoff là cầu nối giúp agent tiếp theo
biết chính xác điều gì đã xảy ra và cần làm gì tiếp theo.

## Cấu trúc Handoff

Mọi handoff đều tuân theo cấu trúc này:

```markdown
# Handoff: [Agent nguồn] → [Agent đích]

## Timestamp: [ISO datetime]
## Feature: FEAT-XXX
## Phase: [phase hiện tại]

## Công việc đã hoàn thành
- [Danh sách bullet các công việc đã làm]
- File đã tạo: [danh sách]
- File đã sửa: [danh sách]

## Việc cần làm tiếp
- [Hướng dẫn cụ thể cho agent tiếp theo]
- [Đầu ra mong đợi]

## Ngữ cảnh cho agent tiếp theo (CẦN ĐỌC GÌ)
- File 1: đường/dẫn/file — [lý do cần đọc]
- File 2: đường/dẫn/file — [lý do cần đọc]
- Convention: CONVENTIONS.md#section — [quy tắc cụ thể cần tuân theo]

## Vấn đề / Blocker
- [Các vấn đề gặp phải]
- [Câu hỏi cần người dùng trả lời, đánh dấu bằng QUESTION:]

## Cập nhật trạng thái
- STATE.md đã cập nhật: [có/không]
- Task card đã cập nhật: [task ID]
```

## Ví dụ Handoff

### Handoff PM → Architect

```markdown
# Handoff: PM → Architect

## Feature: FEAT-003 Product Catalog
## Phase: REQUIREMENT → DESIGN

## Công việc đã hoàn thành
- Tài liệu yêu cầu đã tạo và được người dùng phê duyệt
- Phạm vi: L (ước tính 6-8 file)

## Việc cần làm tiếp
- Thiết kế kiến trúc hệ thống cho product catalog
- Định nghĩa data model, API endpoint, cấu trúc file
- Chia thành các task triển khai (≤ 3 file mỗi task)

## Ngữ cảnh cho agent tiếp theo
- features/FEAT-003/requirement.md — tài liệu yêu cầu đã được phê duyệt
- Chạy: tree -L 3 src/ — hiểu cấu trúc dự án hiện tại
- src/models/ — pattern model hiện có
- src/api/v1/routes/ — pattern route hiện có
- CONVENTIONS.md — tiêu chuẩn code

## Vấn đề / Blocker
- Không có
```

### Handoff Architect → PM → Dev

```markdown
# Handoff: Architect → Backend Dev (qua PM)

## Feature: FEAT-003 Product Catalog
## Phase: DESIGN → IMPLEMENT (Task 1 trên 4)

## Công việc đã hoàn thành
- Tài liệu thiết kế đã hoàn thành: features/FEAT-003/design.md
- Thiết kế đã được người dùng phê duyệt
- 4 task triển khai đã được tạo

## Việc cần làm tiếp
- Triển khai TASK-012: Product model và schema
- Tạo SQLAlchemy model và Pydantic schema

## Ngữ cảnh cho agent tiếp theo
- features/FEAT-003/tasks/TASK-012.md — task card với đầy đủ hướng dẫn
- CONVENTIONS.md#python-models — quy tắc đặt tên model
- CONVENTIONS.md#pydantic — pattern schema
- features/FEAT-003/design.md#data-models — định nghĩa trường

## Vấn đề / Blocker
- Không có
```

### Handoff Dev → Test

```markdown
# Handoff: Backend Dev → Test Agent

## Feature: FEAT-003 Product Catalog
## Phase: IMPLEMENT → TEST (Task 1-4 hoàn thành)

## Công việc đã hoàn thành
- Tất cả 4 task triển khai đã hoàn thành
- File đã tạo:
  - src/models/product.py
  - src/schemas/product.py
  - src/repositories/product_repo.py
  - src/services/product_service.py
  - src/api/v1/routes/products.py
- Tự kiểm tra đã pass trên tất cả file

## Việc cần làm tiếp
- Unit test cho ProductRepository (mock DB)
- Unit test cho ProductService (mock repo)
- Unit test cho Pydantic schema (trường hợp validation)
- Integration test cho API endpoint (test client)

## Ngữ cảnh cho agent tiếp theo
- features/FEAT-003/design.md#api-endpoints — hành vi mong đợi
- src/models/product.py — định nghĩa model
- src/repositories/product_repo.py — các method cần test
- src/services/product_service.py — logic nghiệp vụ cần test
- CONVENTIONS.md#testing — pattern và quy tắc đặt tên test

## Vấn đề / Blocker
- QUESTION: Integration test nên dùng DB thật hay in-memory?
  → PM nên hỏi người dùng
```

### Handoff QA → PM

```markdown
# Handoff: QA → PM

## Feature: FEAT-003 Product Catalog
## Phase: REVIEW → [APPROVED hoặc NEEDS_CHANGES]

## Công việc đã hoàn thành
- Review code toàn bộ đã hoàn thành
- Báo cáo review: features/FEAT-003/reviews/qa-report.md

## Tóm tắt
- Kết luận: NEEDS_CHANGES
- Nghiêm trọng: 1 (thiếu input validation trên endpoint update)
- Cảnh báo: 2 (thiếu structured logging trong tầng service)
- Đề xuất: 3

## Việc cần làm tiếp
- Sửa vấn đề nghiêm trọng trong src/api/v1/routes/products.py:45
- Sửa cảnh báo trong src/services/product_service.py
- Chạy lại QA sau khi sửa

## Ngữ cảnh cho PM
- features/FEAT-003/reviews/qa-report.md — chi tiết review đầy đủ
- Trình cho người dùng để quyết định cuối cùng
```

## Quy tắc đặt tên file Handoff

File handoff hiện tại luôn là `HANDOFF-latest.md`. Khi viết handoff mới, file trước đó được lưu trữ:

```
features/FEAT-XXX/handoffs/
├── HANDOFF-latest.md              ← Hiện tại (PM đọc file này)
├── HANDOFF-001-ba-to-architect.md ← Lưu trữ: Phase 0→2
├── HANDOFF-002-arch-to-dev.md     ← Lưu trữ: Phase 2→3
└── HANDOFF-003-dev-to-test.md     ← Lưu trữ: Phase 3→5
```

**Quy tắc đặt tên lưu trữ**: `HANDOFF-{số thứ tự}-{nguồn}-to-{đích}.md`

PM nên đổi tên `HANDOFF-latest.md` thành tên lưu trữ TRƯỚC KHI viết file mới. Điều này giữ lại lịch sử đồng thời giữ con trỏ "latest" ổn định cho các agent luôn đọc `HANDOFF-latest.md`.

## Quy tắc Handoff

1. **Luôn cập nhật STATE.md** trước khi viết handoff
2. **Chỉ rõ các file** — liệt kê đường dẫn chính xác, không bao giờ viết "các file liên quan"
3. **Giới hạn tham chiếu ngữ cảnh** — tối đa 5-7 file cho agent tiếp theo
4. **Đánh dấu câu hỏi rõ ràng** — thêm tiền tố `QUESTION:` để PM chuyển lên người dùng
5. **Kèm kết quả tự kiểm tra** — agent đã xác thực đầu ra của mình chưa?
6. **Không bao giờ suy đoán** — nếu có điều gì không rõ, hãy ghi lại thay vì đoán
7. **Lưu trữ trước khi ghi đè** — đổi tên HANDOFF-latest.md trước khi viết file mới
