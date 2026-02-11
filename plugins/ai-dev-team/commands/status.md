---
description: Hiển thị trạng thái dự án và tất cả feature song song
allowed-tools: Read, Bash(tree:*), Bash(wc:*)
---

Hiển thị trạng thái dự án hiện tại từ workspace AI Dev Team.

1. Đọc `.ai-workspace/STATE.md` và hiển thị bảng **Parallel Features**

2. Với mỗi feature đang hoạt động, hiển thị thêm:
   - Phase hiện tại và bước tiếp theo
   - Tiến độ task (ví dụ: "3 trên 5 task đã hoàn thành")
   - Thảo luận đang chờ hoặc vấn đề chặn (nếu có)

3. Kiểm tra thư mục `.ai-workspace/discussions/` để tìm xung đột xuyên feature

4. Hiển thị tóm tắt:
   ```
   ## Trạng thái dự án

   | Feature | Phase | Tiến độ | Trạng thái |
   |---------|-------|---------|------------|
   | FEAT-001 Auth | IMPL [3/5] | 60% | IN_PROGRESS |
   | FEAT-002 Catalog | DESIGN | 30% | WAITING_HUMAN |
   | FEAT-003 Orders | CLARIFY | 10% | IN_PROGRESS |

   Thảo luận đang mở: 1 (xung đột file xuyên feature)
   Hành động đề xuất tiếp theo: [gợi ý]
   ```

5. Nếu có xung đột file trong File Conflict Map của STATE.md, nêu bật chúng

6. Nếu `.ai-workspace/` chưa tồn tại, thông báo cho người dùng:
   "Chưa tìm thấy AI workspace. Chạy `/start-project` để khởi tạo."
