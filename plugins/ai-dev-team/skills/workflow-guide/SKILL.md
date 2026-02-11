---
name: workflow-guide
description: >
  Skill này được sử dụng khi người dùng hỏi về "đội AI làm việc thế nào",
  "quy trình làm việc", "vai trò agent", "quy trình bàn giao", "quản lý task",
  "thiết lập dự án", "bắt đầu làm feature", hoặc cần hiểu về quy trình
  phát triển đa agent và mô hình cộng tác người-AI.
version: 0.5.1
---

# Quy trình làm việc của AI Dev Team

Quy trình phát triển đa agent trong đó các AI agent hoạt động như một đội chuyên biệt,
và con người đóng vai trò kép: **Người dùng cuối** (đưa yêu cầu) và **Tech Lead** (giám sát chất lượng).

## Cấu trúc đội ngũ (9 Agent)

```
Human (Người dùng cuối + Tech Lead)
        ↕
   PM Agent ← Điều phối + Quản lý thảo luận
        ↓
  ┌───┬─┼────────┬───────┬────┬─────┐
  ↓   ↓ ↓        ↓       ↓    ↓     ↓
 BA Arch BackDev FrontDev Rev Test   QA
          ↕   ↕    ↕       ↕        ↕
        (thảo luận giữa các agent)

  Researcher ← Theo yêu cầu (agent nào cũng có thể yêu cầu qua PM)
```

| Agent            | Vai trò                                                                      | Đọc                                                       | Ghi                                            |
| ---------------- | ---------------------------------------------------------------------------- | ---------------------------------------------------------- | ---------------------------------------------- |
| **PM**           | Điều phối, phân công, quản lý thảo luận, quản lý feature song song          | STATE.md, stack.config.yaml, handoffs, discussions         | STATE.md, task card, handoffs                  |
| **BA**           | Làm rõ ý tưởng (hỏi đáp), viết requirement                                 | STATE.md, stack.config.yaml, cấu trúc codebase            | features/FEAT-XXX/requirement.md, discussions/ |
| **Architect**    | Thiết kế hệ thống, định nghĩa interface, tạo spec                           | requirement.md, stack.config.yaml, cấu trúc codebase      | features/FEAT-XXX/design.md, discussions/      |
| **Backend Dev**  | Triển khai code backend theo spec và quy ước                                 | task card, stack.config.yaml, CONVENTIONS.md, reviews/     | src/ code backend, discussions/                |
| **Frontend Dev** | Triển khai code frontend theo spec và quy ước                                | task card, stack.config.yaml, CONVENTIONS.md, reviews/     | src/ code frontend, discussions/               |
| **Reviewer**     | Review code đồng nghiệp (chất lượng, pattern, bug)                          | task card, stack.config.yaml, CONVENTIONS.md, src/         | features/FEAT-XXX/reviews/, discussions/       |
| **Tester**       | Viết test, kiểm tra coverage                                                | design.md, stack.config.yaml, src/ code, CONVENTIONS.md    | tests/, discussions/                           |
| **QA**           | Kiểm thử nghiệm thu, bảo mật, sẵn sàng production                          | requirement.md, design.md, stack.config.yaml, src/, tests/ | features/FEAT-XXX/reviews/, discussions/       |
| **Researcher**   | Nghiên cứu kỹ thuật, best practice, so sánh giải pháp                       | stack.config.yaml, cấu trúc codebase, nguồn web           | features/FEAT-XXX/discussions/RESEARCH-XXX.md  |

## Hệ thống Skill động

Cấu hình skill của agent sử dụng hệ thống config hai tầng:

1. **`team.config.yaml`** (cấp plugin, tĩnh) — định nghĩa các _danh mục_ skill mà mỗi agent cần
2. **`.ai-workspace/stack.config.yaml`** (theo dự án, được tạo bởi `/start-project`) — ánh xạ danh mục sang tên thư mục skill thực tế

```
team.config.yaml định nghĩa:
  backend-dev-agent.skills: [backend-patterns, language, conventions]

stack.config.yaml ánh xạ:
  backend-patterns → "fastapi-patterns"
  frontend-guide   → "nextjs16-guide" (hoặc "_none_")
  language         → "python312"
  conventions      → "conventions"

Agent đọc team.config.yaml → lấy danh mục → đọc stack.config.yaml → ánh xạ tên skill
  → tải ${CLAUDE_PLUGIN_ROOT}/skills/{skill_name}/SKILL.md
```

Điều này nghĩa là cùng một bộ agent có thể làm việc với bất kỳ tech stack nào — chỉ cần thay đổi `stack.config.yaml`.

## Workspace chung: `.ai-workspace/`

Tất cả agent giao tiếp qua các file trong `.ai-workspace/`. Mỗi feature có thư mục riêng biệt để thực thi song song:

```
.ai-workspace/
├── STATE.md                 ← Dashboard tổng quan: TẤT CẢ feature, bản đồ xung đột file
├── stack.config.yaml        ← Cấu hình tech stack: ánh xạ danh mục skill sang skill thực tế
├── CONVENTIONS.md           ← Quy tắc viết code chung (tất cả feature tuân theo)
├── CHECKLIST.md             ← Checklist kiểm tra trước khi commit
├── features/                ← Workspace theo feature
│   ├── FEAT-001/            ← Feature Auth (pipeline riêng)
│   │   ├── requirement.md   ← Tài liệu requirement của BA
│   │   ├── design.md        ← Spec thiết kế của Architect
│   │   ├── tasks/           ← Task card triển khai (gắn nhãn backend/frontend)
│   │   │   ├── TASK-001.md
│   │   │   └── TASK-002.md
│   │   ├── reviews/         ← Review đồng nghiệp + báo cáo QA
│   │   │   ├── TASK-001-review.md
│   │   │   └── qa-report.md
│   │   ├── discussions/     ← Thảo luận trong feature + báo cáo nghiên cứu
│   │   │   ├── DISC-001.md
│   │   │   └── RESEARCH-001.md
│   │   └── handoffs/        ← Tài liệu chuyển giao giữa các phase
│   │       └── HANDOFF-latest.md
│   └── FEAT-002/            ← Feature Catalog (pipeline song song)
│       ├── requirement.md
│       ├── design.md
│       ├── tasks/
│       ├── reviews/
│       ├── discussions/
│       └── handoffs/
├── discussions/             ← Thảo luận liên feature
│   └── DISC-CROSS-001.md
└── decisions/               ← Architecture Decision Record chung
    └── ADR-001.md
```

### Quy tắc xử lý feature song song

1. **Cách ly agent**: Khi làm việc trên FEAT-001, TẤT CẢ tham chiếu file trỏ đến `features/FEAT-001/`
2. **Không đọc chéo**: Backend Dev trên FEAT-001 KHÔNG đọc file của FEAT-002
3. **Quy ước chung**: CONVENTIONS.md và stack.config.yaml được chia sẻ — đọc từ thư mục gốc
4. **Phát hiện xung đột**: Trước phase IMPL, PM kiểm tra Bản đồ xung đột file trong STATE.md
5. **Thảo luận liên feature**: Nếu các feature chạm cùng file → mở thảo luận tại `discussions/` gốc

## Quy trình 7 Phase

### Phase 0: CLARIFY → BA Agent (Làm rõ ý tưởng)

1. Con người mô tả feature bằng ngôn ngữ tự nhiên (có thể mơ hồ)
2. PM phân công cho **BA Agent**
3. BA phân tích những gì RÕ RÀNG vs MƠ HỒ vs THIẾU
4. BA đặt **3-7 câu hỏi hướng đích** để làm rõ ý tưởng
5. Con người trả lời → BA hiểu rõ yêu cầu

### Phase 1: REQUIREMENT → BA Agent

1. BA tạo tài liệu requirement có cấu trúc từ ý tưởng đã làm rõ
2. BA viết `features/FEAT-XXX/requirement.md` theo template
3. Requirement bao gồm **Nhật ký làm rõ** — toàn bộ hỏi đáp để truy vết
4. BA bàn giao cho PM → PM trình con người **phê duyệt** → CHECKPOINT

### Phase 2: DESIGN → Architect Agent

1. PM kích hoạt Architect với phạm vi `features/FEAT-XXX/`
2. Architect đọc `features/FEAT-XXX/requirement.md` + quét codebase hiện tại
3. Architect tải skill qua team.config.yaml + stack.config.yaml cho pattern theo framework
4. Architect tạo `features/FEAT-XXX/design.md` với:
   - Sơ đồ component, data model, API contract
   - Danh sách file với trách nhiệm (dùng cho Bản đồ xung đột file)
   - Chia nhỏ thành task triển khai **gắn nhãn `backend` hoặc `frontend`**
5. PM kiểm tra Bản đồ xung đột file → nếu trùng lặp với feature khác, mở thảo luận liên feature
6. PM trình thiết kế cho con người → **CHECKPOINT**

### Phase 3: IMPLEMENT → Backend Dev / Frontend Dev (từng task)

1. PM tạo task card trong `features/FEAT-XXX/tasks/`, mỗi card gắn nhãn `backend`, `frontend`, hoặc `full-stack`
2. PM chuyển mỗi task cho dev agent phù hợp:
   - `backend` → **Backend Dev**
   - `frontend` → **Frontend Dev**
   - `full-stack` → **Backend Dev** trước, sau đó **Frontend Dev**
3. Dev agent CHỈ nhận:
   - Task card (việc cần làm, file nào cần đọc/ghi)
   - Tham chiếu đến phần thiết kế liên quan
   - Skill được tải qua team.config.yaml + stack.config.yaml
4. Dev agent triển khai và tự kiểm tra theo checklist
5. Sau mỗi task → Reviewer review code

### Phase 4: REVIEW → Reviewer Agent (theo từng task)

1. Reviewer đọc task card + code đầu ra
2. Tải skill liên quan qua config (backend hoặc frontend tuỳ nhãn task)
3. Kiểm tra pattern, bug, chất lượng, quy ước
4. Viết `features/FEAT-XXX/reviews/TASK-XXX-review.md` với kết luận
5. Nếu CHANGES_REQUESTED → Dev sửa → Reviewer review lại (tối đa 3 vòng)
6. Nếu APPROVED → chuyển sang task tiếp theo hoặc testing

### Phase 5: TEST → Tester Agent

1. PM kích hoạt Tester với phạm vi `features/FEAT-XXX/`
2. Tester đọc thiết kế (hành vi mong đợi) + code (triển khai thực tế)
3. Tải test framework qua stack.config.yaml (pytest cho Python, vitest cho TypeScript)
4. Tạo unit test, integration test theo quy ước testing
5. Chạy test, báo cáo coverage
6. Nếu test fail → chuyển lại cho Dev phù hợp kèm chi tiết lỗi

### Phase 6: QA → QA Agent → Con người

1. QA đối chiếu feature với **tiêu chí nghiệm thu** từ requirement
2. Tải skill qua config để kiểm tra theo stack cụ thể
3. Chạy kiểm tra bảo mật + sẵn sàng production
4. Tạo `features/FEAT-XXX/reviews/qa-report.md`
5. PM trình báo cáo QA cho con người → **CHECKPOINT CUỐI CÙNG**
6. Con người phê duyệt → Hoàn thành, hoặc yêu cầu chỉnh sửa → quay lại vòng lặp

### Theo yêu cầu: RESEARCH → Researcher Agent

- Bất kỳ agent nào cũng có thể yêu cầu nghiên cứu thông qua PM tại bất kỳ thời điểm nào
- Researcher điều tra câu hỏi kỹ thuật, best practice, so sánh thư viện
- Viết báo cáo `RESEARCH-XXX.md` trong thư mục discussions của feature
- PM chuyển kết quả nghiên cứu đến agent yêu cầu

## Giao tiếp giữa các Agent

Các agent giao tiếp qua **3 kênh** (xem `references/communication-protocol.md`):

### 1. Bàn giao (luồng pipeline)

```
BA → PM → Architect → PM → Backend Dev/Frontend Dev → Reviewer → PM → Tester → PM → QA → PM → Con người
```

### 2. Thảo luận (agent bất kỳ ↔ agent bất kỳ)

Thảo luận trong feature → `features/FEAT-XXX/discussions/DISC-XXX.md`
Thảo luận liên feature → `discussions/DISC-CROSS-XXX.md` tại gốc

Ví dụ:

- Backend Dev hỏi Architect về tính khả thi của thiết kế (trong feature)
- Frontend Dev hỏi Backend Dev về API contract (trong feature)
- Tester hỏi BA để làm rõ tiêu chí nghiệm thu (trong feature)
- Reviewer mở thảo luận với Backend Dev về cách tiếp cận (trong feature)
- QA nêu vấn đề xuyên suốt với mọi người (liên feature)
- PM phát hiện xung đột file giữa FEAT-001 và FEAT-002 (liên feature)
- Bất kỳ agent nào yêu cầu Researcher điều tra câu hỏi kỹ thuật (trong feature)

PM điều phối: chuyển tiếp, hỗ trợ, leo thang (tối đa 3 vòng → con người quyết định).

### 3. Nhận xét review (vòng lặp phản hồi)

- Reviewer → Backend Dev hoặc Frontend Dev: review đồng nghiệp với chu trình sửa/thảo luận/chấp nhận
- QA → agent bất kỳ: phát hiện chất lượng cuối cùng

## Quy tắc quản lý Context

**Vấn đề**: AI có context giới hạn. Tải tất cả = chất lượng kém.

**Giải pháp**: Mỗi agent CHỈ tải những gì cần thiết.

| Agent        | Số file tối đa | Nội dung cần tải                                                                              |
| ------------ | --------------- | --------------------------------------------------------------------------------------------- |
| PM           | 4               | STATE.md, stack.config.yaml, handoff hiện tại, discussions (nếu OPEN)                         |
| BA           | 5               | STATE.md, stack.config.yaml, cấu trúc codebase (ls/tree), requirement hiện có (nếu sửa lại)  |
| Architect    | 5               | tài liệu requirement, stack.config.yaml, cấu trúc hiện tại (ls/tree), interface chính        |
| Backend Dev  | 5               | task card, skill đã ánh xạ, CONVENTIONS.md, file cần sửa                                      |
| Frontend Dev | 5               | task card, skill đã ánh xạ, CONVENTIONS.md, file cần sửa                                      |
| Reviewer     | 5               | task card, skill đã ánh xạ, CONVENTIONS.md, file source đang review                           |
| Tester       | 5               | phần spec, skill đã ánh xạ, file source cần test                                              |
| QA           | 7               | tài liệu requirement, spec, skill đã ánh xạ, CONVENTIONS.md, file source, file test           |
| Researcher   | 7               | stack.config.yaml, cấu trúc codebase, file source liên quan, nguồn web                        |

**Quy tắc giữ context nhỏ gọn**:

1. CONVENTIONS.md sử dụng định dạng checklist, không phải văn xuôi (< 200 dòng)
2. Mỗi task card chứa đúng những gì agent cần + tham chiếu file
3. Spec sử dụng bảng và bullet point, không phải đoạn văn
4. Feature lớn được chia thành task tối đa 3 file mỗi task
5. File bàn giao cung cấp hướng dẫn rõ ràng "cần đọc gì"
6. Thảo luận ngắn gọn: vấn đề + lựa chọn + đề xuất

## Giao thức bàn giao

Khi Agent A hoàn thành và Agent B tiếp nhận (trong một feature):

```
Agent A hoàn thành công việc (trong phạm vi FEAT-XXX)
    → ghi file đầu ra vào features/FEAT-XXX/
    → ghi features/FEAT-XXX/handoffs/HANDOFF-latest.md

PM đọc HANDOFF-latest.md
    → cập nhật STATE.md (dashboard tổng quan)
    → kiểm tra thảo luận đang mở (trong feature + liên feature)
    → xác định bước tiếp theo
    → kích hoạt Agent B trong phạm vi features/FEAT-XXX/
```

Xem `references/handoff-protocol.md` để biết schema bàn giao đầy đủ.

## Các điểm can thiệp của con người

Con người có thể can thiệp bất cứ lúc nào, nhưng BẮT BUỘC phê duyệt tại các checkpoint:

| Checkpoint        | Sau Phase          | Con người xem xét                      | Có thể từ chối?               |
| ----------------- | ------------------ | -------------------------------------- | ----------------------------- |
| CLARIFY           | Làm rõ             | BA đặt câu hỏi, con người trả lời     | N/A — dạng hội thoại          |
| REQ-APPROVE       | Requirement        | Đây có phải điều tôi muốn?            | Có → viết lại                 |
| DESIGN-APPROVE    | Thiết kế           | Kiến trúc có đúng không?              | Có → thiết kế lại             |
| REVIEW-SPOT-CHECK | Mỗi 3 task        | Nhận xét của Reviewer + code           | Có → sửa vấn đề cụ thể       |
| QA-APPROVE        | Báo cáo QA         | Đã sẵn sàng production chưa?          | Có → quay lại vòng lặp        |

## Tài liệu bổ sung

- **`references/workflow-phases.md`** — mô tả chi tiết từng phase kèm ví dụ
- **`references/handoff-protocol.md`** — schema và template bàn giao
- **`references/communication-protocol.md`** — luồng thảo luận, nhận xét review, tương tác giữa agent
- **`references/context-management.md`** — chiến lược quản lý giới hạn context AI
- **`templates/`** — template file cho requirement, spec, task, review, thảo luận
