# AI Dev Team — Claude Code Plugin

Plugin đội phát triển AI đa agent cho [Claude Code](https://claude.com/claude-code). Chín agent chuyên biệt phối hợp theo quy trình có cấu trúc để xây dựng phần mềm — từ phân tích yêu cầu đến kiểm thử QA — với sự giám sát của người dùng tại mọi điểm kiểm tra.

**Các agent:** PM, Business Analyst, Architect, Backend Dev, Frontend Dev, Reviewer, Tester, QA, Researcher

**Tính năng chính:**
- Tự động phát hiện tech stack (FastAPI, Next.js, Django, v.v.)
- Phát triển song song nhiều feature với phát hiện xung đột file
- Thảo luận liên agent và đánh giá code ngang hàng
- Điểm kiểm tra có sự tham gia của người dùng tại mỗi giai đoạn

## Yêu cầu hệ thống

- [Claude Code](https://claude.com/claude-code) phiên bản **1.0.33** trở lên
- Chạy `claude --version` để kiểm tra

## Cài đặt

### Cách A: Giao diện tương tác

1. Mở Claude Code trong terminal
2. Gõ `/plugin` để mở trình quản lý plugin
3. Chuyển sang tab **Marketplaces** (nhấn Tab để chuyển)
4. Thêm marketplace: `dratocoto/ai-dev-team`
5. Chuyển sang tab **Discover**
6. Tìm **ai-dev-team** và nhấn Enter để cài đặt
7. Chọn phạm vi cài đặt (User / Project / Local)

### Cách B: Lệnh CLI

```bash
# Bước 1: Thêm marketplace
/plugin marketplace add dratocoto/ai-dev-team

# Bước 2: Cài đặt plugin
/plugin install ai-dev-team@dratskills
```

Cài đặt ở phạm vi project (chia sẻ với cộng tác viên):

```bash
/plugin install ai-dev-team@dratskills --scope project
```

### Xác nhận cài đặt

Sau khi cài đặt, các lệnh của plugin sẽ khả dụng:

```bash
/ai-dev-team:status
```

### Phát triển local

Để test plugin mà không cần cài qua marketplace:

```bash
claude --plugin-dir ./plugins/ai-dev-team
```

## Bắt đầu nhanh

```bash
# 1. Khởi tạo AI workspace trong dự án
/ai-dev-team:start-project my-app

# 2. Bắt đầu một feature mới
/ai-dev-team:new-feature Add user authentication with JWT

# 3. Theo quy trình: BA làm rõ → bạn duyệt → Architect thiết kế → bạn duyệt → Dev triển khai
```

Đội làm việc theo quy trình 7 giai đoạn: **Clarify → Requirement → Design → Implement → Review → Test → QA**. Bạn duyệt tại mỗi điểm kiểm tra.

## Các lệnh

| Lệnh | Mô tả |
|-------|-------|
| `/ai-dev-team:start-project` | Khởi tạo AI workspace, phát hiện tech stack, cấu hình skills |
| `/ai-dev-team:new-feature` | Bắt đầu feature mới (BA đặt câu hỏi làm rõ trước) |
| `/ai-dev-team:design` | Tạo thiết kế kỹ thuật cho feature hiện tại |
| `/ai-dev-team:implement` | Triển khai task card tiếp theo |
| `/ai-dev-team:test` | Sinh test cho code đã triển khai |
| `/ai-dev-team:review` | Chạy đánh giá QA cho feature |
| `/ai-dev-team:status` | Hiển thị trạng thái dự án và tất cả feature song song |

## Cấu trúc plugin

```
plugins/ai-dev-team/
├── .claude-plugin/plugin.json   # Plugin manifest
├── agents/                      # 9 định nghĩa agent (.md)
├── commands/                    # 7 slash commands
├── skills/                      # Skill packs (workflow-guide, conventions, v.v.)
├── hooks/                       # Trình xử lý sự kiện session (hooks.json)
└── team.config.yaml             # Cấu hình agent tập trung
```

## Thông tin marketplace

- **Tên marketplace:** `dratskills`
- **Tên plugin:** `ai-dev-team`
- **Phiên bản:** 0.5.0

## Quản lý plugin

```bash
# Cập nhật lên phiên bản mới nhất
/plugin update ai-dev-team@dratskills

# Tắt tạm thời
/plugin disable ai-dev-team@dratskills

# Bật lại
/plugin enable ai-dev-team@dratskills

# Gỡ cài đặt
/plugin uninstall ai-dev-team@dratskills
```

## Giấy phép

MIT
