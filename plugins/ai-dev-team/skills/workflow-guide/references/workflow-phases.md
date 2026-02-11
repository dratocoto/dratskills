# Các Phase trong Workflow — Hướng dẫn chi tiết

## Phase 0: Làm rõ ý tưởng (BA Agent)

### Mục đích
BA đặt các câu hỏi có mục tiêu để làm rõ ý tưởng của người dùng TRƯỚC KHI viết bất kỳ tài liệu yêu cầu chính thức nào. Điều này giúp tránh xây dựng sai sản phẩm và tiết kiệm thời gian cho cả team.

### Đầu vào
Mô tả tính năng ban đầu từ người dùng (có thể mơ hồ, ngắn gọn một dòng, hoặc chi tiết).

### Quy trình
1. Đọc STATE.md để hiểu các tính năng hiện có và ngữ cảnh dự án
2. Quét cấu trúc codebase (nếu có) để nắm bối cảnh kỹ thuật
3. Phân tích yêu cầu của người dùng:
   - **RÕ RÀNG**: Những gì được nêu rõ và không mơ hồ
   - **MƠ HỒ**: Những gì được đề cập nhưng cần thêm chi tiết
   - **THIẾU**: Những gì KHÔNG được đề cập nhưng cần thiết cho việc triển khai
4. Tạo 3-7 câu hỏi làm rõ sử dụng Khung câu hỏi QA
5. Đặt câu hỏi cho người dùng → chờ câu trả lời
6. Nếu câu trả lời phát sinh thêm điểm mơ hồ MỚI → 1 vòng hỏi thêm (tối đa 3 câu hỏi)

### Khung câu hỏi QA

Câu hỏi được tổ chức theo danh mục. PM chọn những câu phù hợp nhất — KHÔNG phải tất cả:

**Danh mục 1: AI (Người dùng & Vai trò)**
- Ai sẽ sử dụng tính năng này?
- Có các vai trò người dùng hoặc cấp quyền khác nhau không?
- Có cần authentication/authorization không?

**Danh mục 2: CÁI GÌ (Hành vi cốt lõi)**
- Hành động hoặc luồng chính là gì? (từng bước)
- Dữ liệu đầu vào là gì? Đầu ra là gì?
- "Thành công" trông như thế nào?
- Điều gì xảy ra khi có lỗi hoặc đầu vào không hợp lệ?
- Có cần định dạng response cụ thể không?

**Danh mục 3: PHẠM VI (Giới hạn & Trường hợp biên)**
- Tính năng này KHÔNG bao gồm những gì?
- Có giới hạn nào không? (số bản ghi tối đa, kích thước file, rate limit)
- Điều gì xảy ra với dữ liệu rỗng, null, hoặc bằng không?
- Có cần xử lý truy cập đồng thời không?
- Tính năng có cần idempotent không?

**Danh mục 4: TÍCH HỢP (Ngữ cảnh kỹ thuật)**
- Tính năng này có kết nối với các tính năng hiện có không? Những tính năng nào?
- Có API hoặc dịch vụ bên ngoài nào liên quan không?
- Có nhu cầu thời gian thực không? (WebSocket, SSE, polling)
- Có cần thay đổi database không? (bảng mới, migration)

**Danh mục 5: UX & NGHIỆP VỤ (Nếu áp dụng)**
- Có thành phần UI không? Có pattern ưa thích nào không?
- Yêu cầu sắp xếp, lọc, phân trang?
- Có cần analytics hoặc event tracking không?
- Kỳ vọng về hiệu suất? (thời gian phản hồi, throughput)

### Quy tắc đặt câu hỏi hiệu quả
1. Hỏi **3-7 câu** mỗi vòng — chất lượng hơn số lượng
2. Đặt câu hỏi **quan trọng nhất** lên đầu
3. Cung cấp **các lựa chọn gợi ý** khi có thể:
   - "Phương thức auth: JWT token hay session cookie?"
   - "Định dạng lỗi: RFC 7807 Problem Details hay custom JSON?"
4. Nhóm các câu hỏi liên quan lại với nhau
5. Đánh dấu câu hỏi không thiết yếu là **"Biết thêm thì tốt (tùy chọn):"**
6. Nếu yêu cầu tính năng đã chi tiết → chỉ hỏi **2-3 câu xác nhận**
7. **Tối đa 2 vòng** hỏi đáp — không hỏi dồn dập

### Ví dụ tương tác

```
Human: "I want to add user authentication to the API"

BA: "Trước khi viết tài liệu yêu cầu, tôi cần làm rõ một số điểm:

1. **Phương thức authentication**: JWT token (stateless) hay session-based (server-side)?
2. **Đăng ký người dùng**: Người dùng tự đăng ký, hay chỉ admin tạo?
3. **Quy tắc mật khẩu**: Có yêu cầu cụ thể nào không? (độ dài tối thiểu, độ phức tạp, v.v.)
4. **OAuth/đăng nhập mạng xã hội**: Chỉ email+mật khẩu, hay cả đăng nhập Google/GitHub?
5. **Authorization**: Có các vai trò khác nhau không (admin, user, viewer)?

Biết thêm thì tốt (tùy chọn):
6. Có cần xác minh email cho tài khoản mới không?
7. Có cần luồng đặt lại mật khẩu trong phiên bản này không?"

```

### Đầu ra
Không có file đầu ra chính thức — việc làm rõ mang tính hội thoại. Tuy nhiên, nội dung hỏi đáp được ghi lại trong phần "Nhật ký làm rõ" của tài liệu yêu cầu để đảm bảo truy vết.

### Chuyển sang Phase 1
Khi BA đã có đủ thông tin rõ ràng → tiến hành viết tài liệu yêu cầu ngay. Không cần checkpoint riêng — việc làm rõ CHÍNH LÀ checkpoint.

---

## Phase 1: Phân tích yêu cầu (BA Agent)

### Đầu vào
Ý tưởng tính năng đã được làm rõ (từ hỏi đáp Phase 0) + yêu cầu gốc của người dùng.

### Quy trình
1. Tổng hợp yêu cầu gốc + câu trả lời làm rõ thành một yêu cầu mạch lạc
2. Xác định: AI cần nó, NÓ làm gì, TẠI SAO cần (bây giờ đã có câu trả lời rõ ràng)
3. Chia thành user story với tiêu chí chấp nhận (bao gồm các trường hợp biên từ hỏi đáp)
4. Xác định ràng buộc kỹ thuật và phụ thuộc
5. Ước lượng phạm vi: S (1-2 file), M (3-5 file), L (6-10 file), XL (10+ file)
6. Đưa toàn bộ Nhật ký làm rõ vào tài liệu yêu cầu

### Đầu ra: `features/FEAT-XXX/requirement.md`

```markdown
# FEAT-XXX: [Tên tính năng]

## Trạng thái: DRAFT → APPROVED
## Phạm vi: M (ước tính 4-5 file)
## Độ ưu tiên: HIGH

## Mô tả vấn đề
[1-2 câu: vấn đề này giải quyết điều gì?]

## User Story
1. Với vai trò [vai trò], tôi muốn [hành động] để [lợi ích]
2. ...

## Tiêu chí chấp nhận
- [ ] AC-1: [tiêu chí cụ thể, có thể kiểm tra]
- [ ] AC-2: ...

## Ràng buộc kỹ thuật
- Phải sử dụng async cho tất cả thao tác DB
- Phải tuân theo pattern auth middleware hiện có
- API phải tương thích ngược

## Ngoài phạm vi
- [Tính năng này KHÔNG bao gồm gì]

## Phụ thuộc
- Phụ thuộc vào: [module/tính năng hiện có]
- Chặn: [tính năng đang chờ tính năng này]

## Nhật ký làm rõ
Câu hỏi đã đặt và câu trả lời nhận được trong Phase 0:

| # | Câu hỏi | Trả lời của người dùng |
|---|---------|----------------------|
| 1 | Phương thức auth: JWT hay session? | JWT stateless |
| 2 | Tự đăng ký hay chỉ admin? | Tự đăng ký có xác minh email |
| 3 | Quy tắc mật khẩu? | Tối thiểu 8 ký tự, 1 chữ hoa, 1 số |
| 4 | Cần OAuth không? | Không trong phiên bản này |
| 5 | Vai trò người dùng? | Admin + người dùng thường |
```

### Checkpoint: REQ-APPROVE
BA chuyển tài liệu yêu cầu cho PM. PM trình cho người dùng:
- "BA đã chuẩn bị tài liệu yêu cầu dựa trên cuộc thảo luận. Đây là nội dung:"
- "Ước lượng phạm vi là M (4-5 file). Có phù hợp với kỳ vọng của bạn không?"
- Nhấn mạnh các quyết định chính được đưa ra trong quá trình làm rõ
- Người dùng phê duyệt, chỉnh sửa, hoặc từ chối.

---

## Phase 2: Thiết kế kỹ thuật (Architect Agent)

### Đầu vào
Tài liệu yêu cầu đã được phê duyệt + cấu trúc codebase hiện có.

### Quy trình
1. Đọc tài liệu yêu cầu
2. Quét codebase hiện có: `tree -L 3 src/` để hiểu cấu trúc
3. Đọc các file interface quan trọng (model, router, service) để hiểu pattern
4. Thiết kế component, luồng dữ liệu, API contract
5. Chia thành các task triển khai (mỗi task ≤ 3 file)

### Đầu ra: `features/FEAT-XXX/design.md`

```markdown
# Thiết kế FEAT-XXX: [Tên tính năng]

## Tổng quan kiến trúc
[Sơ đồ Mermaid thể hiện mối quan hệ giữa các component]

## Data Model

### NewModel
| Trường | Kiểu | Bắt buộc | Mô tả |
|--------|------|----------|-------|
| id | UUID | tự động | Khóa chính |
| name | str | có | Tên hiển thị |

## API Endpoint

### POST /api/v1/resource
- Auth: Bắt buộc (Bearer token)
- Request Body: CreateResourceSchema
- Response 201: ResourceResponse
- Response 400: ValidationError
- Response 401: UnauthorizedError

### GET /api/v1/resource/{id}
...

## Thay đổi file

| File | Hành động | Mô tả |
|------|-----------|-------|
| src/models/resource.py | TẠO MỚI | SQLAlchemy model |
| src/schemas/resource.py | TẠO MỚI | Pydantic schema |
| src/repositories/resource_repo.py | TẠO MỚI | Tầng truy cập dữ liệu |
| src/services/resource_service.py | TẠO MỚI | Logic nghiệp vụ |
| src/api/v1/routes/resource.py | TẠO MỚI | API endpoint |

## Task triển khai

### TASK-001: Data Model và Schema
- Tạo: src/models/resource.py, src/schemas/resource.py
- Đọc: CONVENTIONS.md#python-models

### TASK-002: Tầng Repository
- Tạo: src/repositories/resource_repo.py
- Đọc: src/repositories/base.py (làm theo pattern)

### TASK-003: Tầng Service
- Tạo: src/services/resource_service.py
- Đọc: file đầu ra của TASK-001

### TASK-004: API Endpoint
- Tạo: src/api/v1/routes/resource.py
- Đọc: src/api/v1/routes/ (làm theo pattern hiện có)
- Sửa: src/api/v1/router.py (đăng ký route mới)

## Quyết định thiết kế
- [Tại sao chọn cách tiếp cận này thay vì các phương án khác]
```

### Checkpoint: DESIGN-APPROVE
Architect trình bày cho người dùng thông qua PM:
- Sơ đồ component
- Danh sách file kèm mô tả
- Phân chia task
- Người dùng phê duyệt kiến trúc hoặc đề xuất thay đổi.

---

## Phase 3: Triển khai (Backend Dev / Frontend Dev, từng task một)

### Đầu vào
Từng task card một. Dev KHÔNG BAO GIỜ tải toàn bộ thiết kế — chỉ task card.

### Quy trình (cho mỗi task)
1. Đọc task card để nắm phạm vi và hướng dẫn
2. Đọc các quy tắc convention liên quan
3. Đọc các file hiện có mà task cần tham chiếu/sửa đổi
4. Viết code tuân theo convention
5. Tự kiểm tra đối chiếu CHECKLIST.md
6. Cập nhật trạng thái task

### Task Card: `features/FEAT-XXX/tasks/TASK-XXX.md`

```markdown
# TASK-XXX: [Tiêu đề task]

## Feature: FEAT-XXX
## Agent: backend-dev-agent / frontend-dev-agent
## Label: backend / frontend / full-stack
## Trạng thái: TODO → IN_PROGRESS → DONE

## Việc cần làm
[Mô tả rõ ràng, có thể hành động được]

## File cần đọc (Ngữ cảnh)
- CONVENTIONS.md#python-models (quy tắc đặt tên và pattern)
- src/repositories/base.py (làm theo pattern này)
- features/FEAT-XXX/design.md#data-models (định nghĩa trường)

## File cần tạo/sửa
- TẠO MỚI: src/models/resource.py
- TẠO MỚI: src/schemas/resource.py

## Tiêu chí chấp nhận
1. Model có đầy đủ các trường theo thiết kế
2. Pydantic schema cho Create, Update, Response
3. Type hint trên tất cả hàm
4. Tương thích async

## Tự kiểm tra trước khi hoàn thành
- [ ] Type hint đầy đủ
- [ ] Không có giá trị hardcode
- [ ] Có xử lý lỗi
- [ ] Tuân theo quy tắc đặt tên
- [ ] Không còn TODO/FIXME
```

### Quy tắc cho Dev
- CHỈ đọc các file được liệt kê trong task card
- Tải skill thông qua team.config.yaml + stack.config.yaml (backend-patterns hoặc frontend-guide)
- LUÔN LUÔN tuân theo pattern trong CONVENTIONS.md
- LUÔN LUÔN tự kiểm tra trước khi đánh dấu hoàn thành
- Nếu không chắc về một quyết định thiết kế → thêm comment `# QUESTION:` và báo cho PM

---

## Phase 4: Peer Review (Reviewer Agent)

### Mục đích
Sau khi Dev hoàn thành một task, Reviewer thực hiện peer code review. Bước này giúp phát hiện lỗi, vi phạm pattern, và vấn đề chất lượng TRƯỚC KHI chạy bộ test đầy đủ. Đây là review theo từng task, không phải theo feature.

### Đầu vào
Code đã hoàn thành + task card + CONVENTIONS.md.

### Quy trình
1. Đọc task card để hiểu những gì đã được xây dựng
2. Đọc các file mã nguồn do Dev tạo ra
3. Tải skill liên quan thông qua team.config.yaml + stack.config.yaml (backend hoặc frontend tùy theo label của task)
4. Kiểm tra: chất lượng code, đặt tên, pattern, lỗi, convention, bảo mật cơ bản
5. Viết review: `features/FEAT-XXX/reviews/TASK-XXX-review.md`

### Đầu ra: `features/FEAT-XXX/reviews/TASK-XXX-review.md`

```markdown
# Review: TASK-XXX [Tiêu đề task]

## Kết luận: APPROVED / CHANGES_REQUESTED

## Tóm tắt
- File đã review: N
- Vấn đề phát hiện: N

## Vấn đề (bắt buộc sửa)
1. [file:line] — Mô tả → Gợi ý sửa

## Đề xuất (nên có)
1. [file:line] — Mô tả → Ý tưởng cải thiện
```

### Luồng xử lý
- **APPROVED** → PM chuyển task tiếp theo (nếu còn) hoặc chuyển sang phase TEST
- **CHANGES_REQUESTED** → Dev sửa → Reviewer review lại (tối đa 3 vòng)
- **Vượt quá 3 vòng** → Chuyển lên người dùng

### Quy tắc
1. Reviewer KHÔNG viết code — chỉ review và đề xuất
2. Một review cho mỗi task, không phải mỗi file
3. Review phải chỉ rõ vị trí file:line cụ thể
4. Reviewer tải cùng skill profile như Dev (backend hoặc frontend)

---

## Phase 5: Kiểm thử (Test Agent)

### Đầu vào
Các file code đã hoàn thành + phần spec liên quan.

### Quy trình
1. Đọc phần spec cho hành vi mong đợi
2. Đọc code triển khai
3. Tạo test tuân theo convention kiểm thử
4. Chạy test và báo cáo kết quả
5. Nếu coverage < 80% cho logic nghiệp vụ → tạo thêm test

### Đầu ra
- Các file test trong `tests/`
- Báo cáo coverage trong handoff

### Ánh xạ Test-Code
Với mỗi file mã nguồn, Test Agent tạo:
- `tests/unit/test_<module>.py` — unit test với dependency được mock
- `tests/integration/test_<module>_integration.py` — test với DB thật (nếu áp dụng)

---

## Phase 6: QA (QA Agent)

### Đầu vào
Tất cả file mã nguồn mới/đã sửa + file test + convention.

### Quy trình
1. Đọc CONVENTIONS.md và CHECKLIST.md
2. Với mỗi file mã nguồn, kiểm tra:
   - Chất lượng code (đặt tên, cấu trúc, độ phức tạp)
   - Bảo mật (pattern OWASP)
   - Xử lý lỗi (tính đầy đủ)
   - An toàn kiểu (type annotation)
   - Độ phủ test (các trường hợp biên đã được test chưa?)
3. Tạo báo cáo review có cấu trúc

### Đầu ra: `features/FEAT-XXX/reviews/qa-report.md`

```markdown
# Review: FEAT-XXX [Tên tính năng]

## Kết luận: APPROVED / NEEDS_CHANGES / REJECTED

## Tóm tắt
- File đã review: N
- Vấn đề nghiêm trọng: N
- Cảnh báo: N
- Đề xuất: N

## Nghiêm trọng (bắt buộc sửa)
1. [file:line] — Mô tả → Gợi ý sửa

## Cảnh báo (nên sửa)
1. [file:line] — Mô tả → Gợi ý sửa

## Đề xuất (nên có)
1. [file:line] — Mô tả → Ý tưởng cải thiện

## Đánh giá độ phủ test
- Độ phủ logic nghiệp vụ: X%
- Test case còn thiếu: [danh sách]

## Sẵn sàng production
- [ ] Không có secret hardcode
- [ ] Tất cả đầu vào đã được validate
- [ ] Lỗi được xử lý đúng cách
- [ ] Có logging (structured)
- [ ] Không có TODO/FIXME trong đường dẫn production
```

### Checkpoint cuối cùng: QA-APPROVE
PM trình báo cáo QA cho người dùng. Người dùng đưa ra quyết định cuối cùng.
