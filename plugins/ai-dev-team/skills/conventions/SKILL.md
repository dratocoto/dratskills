---
name: conventions
description: >
  Skill này được sử dụng khi người dùng hỏi về "quy ước viết code",
  "chuẩn code", "quy tắc đặt tên", "quy tắc dự án", hoặc khi bất kỳ
  agent nào cần kiểm tra các chuẩn viết code trước khi code. Đây là bộ
  quy tắc gọn nhẹ mà tất cả agent tham chiếu.
version: 0.1.0
---

# Quy ước dự án

Bộ quy tắc gọn nhẹ, dễ tra cứu cho tất cả AI agent. Sử dụng định dạng checklist để tối ưu context.

## Quy ước Python / FastAPI

### Đặt tên
- [ ] `snake_case` — hàm, biến, module, file
- [ ] `PascalCase` — class, Pydantic model, exception
- [ ] `SCREAMING_SNAKE` — hằng số
- [ ] `_prefixed` — private/nội bộ

### Model (SQLAlchemy)
- [ ] Kế thừa từ `Base` (declarative base)
- [ ] Khoá chính `UUID` (`id = Column(UUID, primary_key=True, default=uuid4)`)
- [ ] Bao gồm cột `created_at`, `updated_at`
- [ ] Tên bảng = số nhiều viết thường (`__tablename__ = "users"`)
- [ ] Type hint cho tất cả cột
- [ ] Relationship sử dụng `selectin` lazy loading

### Schema (Pydantic)
- [ ] Kế thừa từ `BaseModel` với `model_config = ConfigDict(from_attributes=True)`
- [ ] Tách riêng: schema `Create*`, `Update*`, `*Response`
- [ ] Schema `Update*`: tất cả field là `Optional` (cập nhật một phần)
- [ ] Validate field với ràng buộc `Field()`
- [ ] Sử dụng `UUID` thay vì `str` cho trường ID

### Repository
- [ ] Kế thừa từ `BaseRepository[ModelT]`
- [ ] TẤT CẢ method là `async def`
- [ ] Nhận `AsyncSession` qua DI
- [ ] Trả về domain model, không trả dict
- [ ] Sử dụng `select()` builder, không dùng raw SQL string
- [ ] Bao gồm phân trang: tham số `offset` + `limit`

### Service
- [ ] Nhận repository qua `__init__`
- [ ] TẤT CẢ method là `async def`
- [ ] Raise domain exception (`NotFoundError`, `ConflictError`)
- [ ] Không chứa logic HTTP/request (không dùng `Request`, `Response`)
- [ ] Validate business rule trước khi lưu dữ liệu
- [ ] Log các hành động quan trọng với `structlog`

### Route
- [ ] Sử dụng `APIRouter` với `prefix` và `tags`
- [ ] Nhận service qua `Depends()`
- [ ] Sử dụng Pydantic schema cho kiểu request/response
- [ ] Trả về Pydantic model (tự động serialize)
- [ ] Khai báo `response_model` cho mọi endpoint
- [ ] Bao gồm `status_code` cho create (201), delete (204)

### Xử lý lỗi
- [ ] Hệ thống exception tuỳ chỉnh kế thừa từ `AppError`
- [ ] Đăng ký `exception_handler` cho `AppError` trong main.py
- [ ] Không bao giờ trả stack trace trong API response
- [ ] Log đầy đủ context lỗi với `logger.error()`
- [ ] Bọc lỗi từ bên ngoài trong domain exception

### Async
- [ ] `async def` cho TẤT CẢ thao tác I/O
- [ ] Sử dụng driver `asyncpg` (không dùng `psycopg2`)
- [ ] Sử dụng `httpx.AsyncClient` (không dùng `requests`)
- [ ] Sử dụng `aiofiles` cho file I/O
- [ ] Sử dụng `asyncio.gather()` cho các thao tác đồng thời
- [ ] KHÔNG BAO GIỜ block event loop bằng lệnh gọi đồng bộ

### Logging
- [ ] Sử dụng `structlog` (không dùng `logging` hay `print`)
- [ ] Định dạng key-value có cấu trúc
- [ ] Cấp độ: ERROR (hỏng), WARN (suy giảm), INFO (sự kiện), DEBUG (phát triển)
- [ ] KHÔNG BAO GIỜ log mật khẩu, token, PII
- [ ] Bao gồm correlation/request ID

## Quy ước TypeScript / Next.js

### Đặt tên
- [ ] `camelCase` — biến, hàm
- [ ] `PascalCase` — component, type, interface
- [ ] `SCREAMING_SNAKE` — hằng số
- [ ] `kebab-case` — tên file

### Component
- [ ] Mặc định là Server Components (không cần directive)
- [ ] Thêm `"use client"` CHỈ KHI cần tương tác
- [ ] Khai báo Props interface phía trên component
- [ ] Sử dụng `"use cache"` cho server component nặng dữ liệu

### Type
- [ ] `strict: true` trong tsconfig
- [ ] Khai báo kiểu trả về rõ ràng cho hàm exported
- [ ] `interface` cho object shape, `type` cho union/intersection
- [ ] `unknown` thay vì `any`; thu hẹp kiểu với type guard

### State & Dữ liệu
- [ ] Server Components: fetch dữ liệu async trực tiếp
- [ ] Client Components: SWR hoặc React Query
- [ ] Form: Server Actions với `"use server"`
- [ ] State trên URL: `useSearchParams` cho view có bộ lọc

## Quy ước Testing

### Tất cả ngôn ngữ
- [ ] Đặt tên file test: `test_<module>.py` / `*.test.ts`
- [ ] Pattern Arrange-Act-Assert
- [ ] Tên mô tả rõ ràng: `test_<unit>_<scenario>_<expected>`
- [ ] Chỉ mock dependency bên ngoài
- [ ] Cả test case positive VÀ negative
- [ ] Test thông báo lỗi và kiểu lỗi
- [ ] Coverage >= 80% cho business logic
- [ ] Các test độc lập và idempotent

## Quy ước Git

- [ ] Branch: `feature/FEAT-XXX-description`, `fix/BUG-XXX-description`
- [ ] Commit: Conventional Commits (`feat:`, `fix:`, `refactor:`, `test:`, `docs:`)
- [ ] PR: link tới tài liệu requirement của feature
- [ ] Không bao giờ commit `.env`, secret, hoặc file binary lớn
