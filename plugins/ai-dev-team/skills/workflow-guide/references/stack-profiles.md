# Stack Profile

Các ánh xạ skill được định nghĩa sẵn cho các tech stack phổ biến.
Được sử dụng bởi `/start-project` để tự động tạo `.ai-workspace/stack.config.yaml`.

## Profile: fastapi-nextjs (mặc định)

| Danh mục | Tên Skill |
|----------|-----------|
| backend-patterns | fastapi-patterns |
| frontend-guide | nextjs16-guide |
| language | python312 |
| conventions | conventions |

**Phát hiện**: `pyproject.toml` chứa `fastapi` VÀ `package.json` chứa `next`

## Profile: fastapi-only

| Danh mục | Tên Skill |
|----------|-----------|
| backend-patterns | fastapi-patterns |
| frontend-guide | _none_ |
| language | python312 |
| conventions | conventions |

**Phát hiện**: `pyproject.toml` chứa `fastapi`, không có `package.json`

## Profile: nextjs-only

| Danh mục | Tên Skill |
|----------|-----------|
| backend-patterns | _none_ |
| frontend-guide | nextjs16-guide |
| language | _none_ |
| conventions | conventions |

**Phát hiện**: `package.json` chứa `next`, không có `pyproject.toml`

## Profile: django-react

| Danh mục | Tên Skill | Trạng thái |
|----------|-----------|------------|
| backend-patterns | django-patterns | Chưa tạo |
| frontend-guide | react-guide | Chưa tạo |
| language | python312 | Sẵn có |
| conventions | conventions | Sẵn có |

**Phát hiện**: `requirements.txt` hoặc `pyproject.toml` chứa `django` VÀ `package.json` chứa `react`

> `django-patterns` và `react-guide` chưa có thư mục skill. Tạo chúng tại `${CLAUDE_PLUGIN_ROOT}/skills/{name}/SKILL.md` để kích hoạt profile này. Nếu chưa có, agent sẽ bỏ qua các danh mục skill đó.

## Profile: express-vue

| Danh mục | Tên Skill | Trạng thái |
|----------|-----------|------------|
| backend-patterns | express-patterns | Chưa tạo |
| frontend-guide | vue-guide | Chưa tạo |
| language | _none_ | — |
| conventions | conventions | Sẵn có |

**Phát hiện**: `package.json` chứa `express` VÀ `vue`

> `express-patterns` và `vue-guide` chưa có thư mục skill. Tạo chúng tại `${CLAUDE_PLUGIN_ROOT}/skills/{name}/SKILL.md` để kích hoạt profile này. Nếu chưa có, agent sẽ bỏ qua các danh mục skill đó.

## Thứ tự ưu tiên phát hiện tự động

1. Kiểm tra `pyproject.toml` → trích xuất backend framework (fastapi, django, flask)
2. Kiểm tra `requirements.txt` → phương án dự phòng cho dự án Python
3. Kiểm tra `package.json` → trích xuất frontend framework (next, react, vue, angular)
4. Kiểm tra `package.json` → trích xuất backend framework (express, nestjs, hono)
5. Nếu không khớp → hỏi người dùng chọn hoặc dùng profile chung

## Thêm Profile mới

1. Tạo thư mục skill: `${CLAUDE_PLUGIN_ROOT}/skills/{framework}-patterns/SKILL.md`
2. Thêm mục profile trong file này
3. Thêm quy tắc phát hiện
4. Profile sẽ có sẵn trong `/start-project`

## Giá trị đặc biệt: _none_

Khi một danh mục được đặt thành `_none_`, agent sẽ bỏ qua việc tải skill đó hoàn toàn.
Điều này dành cho các dự án không có frontend (chỉ API) hoặc không cần pattern backend theo framework cụ thể.
