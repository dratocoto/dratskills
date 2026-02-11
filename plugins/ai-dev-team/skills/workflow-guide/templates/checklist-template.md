# Checklist chất lượng trước khi commit

Sử dụng checklist này trước khi đánh dấu bất kỳ task nào là DONE. Agent Dev tự kiểm tra; Reviewer xác minh lại.

## Chất lượng code
- [ ] Tất cả functions đều có type hints (params + return)
- [ ] Không còn `# TODO` hoặc `# FIXME` trong production code
- [ ] Không có secrets, URLs, hoặc magic numbers bị hardcode
- [ ] Xử lý lỗi: domain exceptions, không dùng bare `except`
- [ ] Tuân thủ quy ước đặt tên (snake_case Python, camelCase TS)

## Async & I/O (nếu áp dụng)
- [ ] Tất cả thao tác DB sử dụng `async def`
- [ ] Không có blocking I/O trong async functions
- [ ] Dọn dẹp connection/session đúng cách (context managers)

## Validation & Security
- [ ] Tất cả API inputs được validate qua Pydantic schemas (hoặc tương đương)
- [ ] Không có nguy cơ SQL injection (chỉ dùng parameterized queries)
- [ ] Không có dữ liệu nhạy cảm trong logs hoặc thông báo lỗi
- [ ] Kiểm tra authentication/authorization ở những nơi cần thiết

## Logging & Observability
- [ ] Sử dụng structured logging (structlog / pino / v.v.)
- [ ] Không dùng `print()` để logging
- [ ] Các thao tác quan trọng được log (create, update, delete, errors)

## Patterns & Conventions
- [ ] Tuân thủ các pattern hiện có trong dự án (kiểm tra CONVENTIONS.md)
- [ ] Tuân thủ các pattern đặc thù framework (kiểm tra resolved skill)
- [ ] Đặt tên file theo quy ước dự án
- [ ] Thứ tự import theo quy ước

## Sẵn sàng kiểm thử
- [ ] Code có thể kiểm thử được (dependencies injectable)
- [ ] Không có global state gây cản trở isolated testing
- [ ] Các edge cases đã được xem xét (empty, null, boundary values)
