# TASK-XXX: [Tiêu đề task]

## Feature: FEAT-XXX
## Agent: backend-dev-agent / frontend-dev-agent / test-agent
## Label: backend / frontend / full-stack
## Status: TODO / IN_PROGRESS / DONE / BLOCKED
## Số file dự kiến: N

## Việc cần làm
[Mô tả rõ ràng, có thể hành động, 2-3 câu]

## File cần đọc (Ngữ cảnh)
1. `CONVENTIONS.md#[section]` — [lý do: quy tắc nào cần tuân thủ]
2. `features/FEAT-XXX/design.md#[section]` — [lý do: định nghĩa field / API contracts]
3. `src/path/to/existing-pattern.py` — [lý do: tuân theo pattern này]

## File cần tạo hoặc sửa
- CREATE: `src/path/to/new_file.py`
- MODIFY: `src/path/to/existing_file.py` — [nội dung cần thay đổi]

## Tiêu chí chấp nhận
1. [tiêu chí cụ thể]
2. [tiêu chí cụ thể]
3. [tiêu chí cụ thể]

## Tự kiểm tra trước khi đánh dấu Done
- [ ] Type hints trên tất cả function signatures
- [ ] Async def cho các thao tác I/O
- [ ] Xử lý lỗi với domain-specific exceptions
- [ ] Structured logging (không dùng print)
- [ ] Không có giá trị hardcode hoặc secrets
- [ ] Tuân thủ quy ước đặt tên từ CONVENTIONS.md
- [ ] Không còn TODO/FIXME trong code
