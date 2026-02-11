# Trạng thái dự án

## Project: [Tên dự án]
## Stack: Xem stack.config.yaml
## Cập nhật lần cuối: [ngày]

## Các tính năng song song

| ID | Tính năng | Phase | Phân công | Tiến độ | Trạng thái | Bị chặn bởi |
|----|-----------|-------|-----------|---------|------------|--------------|
| - | - | - | - | - | - | - |

## Chú giải Phase
- `CLARIFY` → BA đang đặt câu hỏi
- `REQ` → BA đang viết requirement
- `DESIGN` → Architect đang thiết kế
- `IMPL [2/5]` → Dev đang triển khai (task 2 trên 5)
- `REVIEW [2/5]` → Reviewer đang review (task 2 trên 5)
- `TEST` → Tester đang viết tests
- `QA` → QA kiểm tra cuối cùng
- `DONE` → Người dùng đã duyệt

## Chú giải trạng thái
- IN_PROGRESS → đang được thực hiện
- WAITING_HUMAN → cần người dùng duyệt/cung cấp thông tin
- WAITING_AGENT → đang chờ agent khác
- BLOCKED → bị chặn bởi tính năng khác hoặc phụ thuộc bên ngoài
- DONE → hoàn thành và đã được duyệt

## Bản đồ xung đột file

Theo dõi các source file mà mỗi tính năng thay đổi để phát hiện xung đột:

| Source File | FEAT-001 | FEAT-002 | FEAT-003 | Xung đột? |
|------------|----------|----------|----------|-----------|
| - | - | - | - | - |

Khi phát hiện xung đột → mở cross-feature discussion trong `discussions/`

## Hoạt động gần đây
- [thời gian] Dự án được khởi tạo
