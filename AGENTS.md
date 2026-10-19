# AGENTS.md — Bản Đồ Điều Hướng Trung Tâm Cho AI Agent

> **ĐIỂM VÀO BẮT BUỘC (MANDATORY ENTRY POINT):** Mọi AI Agent (Claude Code, Antigravity, Cursor, Codex) khi khởi động phiên làm việc mới trong kho mã nguồn này **BẮT BUỘC** đọc tệp này đầu tiên để nắm rõ ranh giới kiến trúc, hệ thống quy tắc và quy trình vận hành.

---

## 1. Kiến Trúc Component-First Đồng Nhất (`src/`) & Ranh Giới Trách Nhiệm

Dự án sử dụng kiến trúc **Unified Component-First 3 Tầng** trong `src/` (tuyệt đối không xé vụn thành nhiều repo/monorepo rời rạc gây trùng lặp `node_modules` và UI components):

| Phân khu | Vai trò & Trách nhiệm | Nguyên tắc bất biến |
| :--- | :--- | :--- |
| [`src/components/ui/`](./src/README.md) | **Tầng 1 (UI Primitives / Atoms):** `Button`, `Input`, `Modal`, `Skeleton`, `Badge`. | Thuần giao diện, nhận props để render. **CẤM** chứa logic nghiệp vụ, gọi API hoặc import DB. |
| [`src/features/`](./src/README.md) | **Tầng 2 (Feature Modules):** `src/features/<feature>/` (chứa `components/`, `hooks/`, `schemas/`). | Xử lý nghiệp vụ cụ thể, validate form (Zod + RHF), đủ 4 trạng thái UI (`Loading`, `Error`, `Empty`, `Success`). |
| [`src/app/`](./src/README.md) | **Tầng 3 (Page Orchestrator & Route Groups):** `(marketing)/`, `(portal)/`, `(admin)/`, `api/`. | File `page.tsx` **chỉ điều phối (< 80 dòng)**, mặc định là Server Component; đẩy `'use client'` xuống tận ngọn lá (Leaf). |
| [`src/server/`](./src/README.md) | **Backend & Data Layer:** Services, Repositories, Prisma/Drizzle Schema & Migrations, Env Validation (`src/lib/`). | Chuẩn hóa thời gian UTC ISO-8601 (`Z`), Soft Delete, Expand & Contract Migration, Log JSON 13 trường. |
| [`tests/`](./tests/README.md) | **Kiểm thử tự động:** Playwright E2E (ưu tiên Mobile `375px/390px`), Unit & Integration Test. | Bắt locator theo `data-testid` (`[feature]__[component]--[type]`). Cấm sửa code `src/` để ép test pass. |
| [`docs/`](./docs/) | **Kho tài liệu sống:** Lưu trữ toàn bộ tài liệu `.md` của dự án theo 6 phân khu. | **CẤM** tạo file `.md` báo cáo hay tài liệu rời rạc ngoài thư mục gốc; bắt buộc đặt đúng phân khu trong `docs/`. |

---

## 2. Hệ Thống Điều Khiển AI Trong `.claude/` & `.agents/`

1. **Luật Bất Biến Luôn Kích Hoạt (`.claude/rules/` & `.agents/rules/`):**
   - [`folder-structure.md`](./.claude/rules/folder-structure.md): Quy tắc phân bổ thư mục code và tài liệu `docs/`.
   - [`roadmap-signature.md`](./.claude/rules/roadmap-signature.md): Tự động đối chiếu và cập nhật [`docs/ROADMAP.md`](./docs/ROADMAP.md) & [`docs/CHANGELOG.md`](./docs/CHANGELOG.md) sau mỗi tính năng.
   - [`architecture-signature.md`](./.claude/rules/architecture-signature.md): Tự động đồng bộ kiến trúc vào [`docs/technical/ARCHITECTURE_PLAN.md`](./docs/technical/ARCHITECTURE_PLAN.md) khi thêm/sửa module.
   - [`permission-gate.md`](./.claude/rules/permission-gate.md): Cổng kiểm soát quyền hạn, cấm lệnh hủy diệt, tích hợp phản xạ chấm điểm rủi ro JEV (`0.0 – 1.0`).
   - [`token-guard.md`](./.claude/rules/token-guard.md): Kỷ luật tiết kiệm token và cơ chế **Fast-Track Bypass** cho các chỉnh sửa nhỏ (`< 50 dòng`).
   - [`responsive-ui.md`](./.claude/rules/responsive-ui.md): Tiêu chuẩn Mobile-First, vùng chạm `44x44px`, chống tràn ngang và đối chiếu giao diện 100%.

2. **Lệnh Slash Chủ Động (`.claude/commands/`):**
   - `/build`: Đọc [`docs/product/BUSINESS_FLOW.md`](./docs/product/BUSINESS_FLOW.md) và [`docs/ROADMAP.md`](./docs/ROADMAP.md) để triển khai từng Sprint.
   - `/architecture-plan`: Quét toàn bộ mã nguồn và hạ tầng để cập nhật 27 phần trong [`docs/technical/ARCHITECTURE_PLAN.md`](./docs/technical/ARCHITECTURE_PLAN.md).
   - `/audit-blueprint`: Phỏng vấn 4 câu hỏi ngữ cảnh và mổ xẻ hệ thống mẫu vào [`docs/product/AUDIT_BLUEPRINT.md`](./docs/product/AUDIT_BLUEPRINT.md).
   - `/context-handoff`: Đóng gói hoặc tiếp nhận bàn giao ngữ cảnh 6 mục qua [`docs/ops/HANDOFF.md`](./docs/ops/HANDOFF.md).
   - `/clean-room`: Chạy quy trình tái cấu trúc độc lập (REA $\rightarrow$ JEV $\rightarrow$ Opus $\rightarrow$ Haiku) dựa trên [`docs/specs/SPEC.md`](./docs/specs/SPEC.md).
   - `/diagram-design`: Xuất sơ đồ kiến trúc và luồng dữ liệu trực quan.

3. **Kỹ Năng Chuyên Sâu Nạp Theo Ngữ Cảnh (`.claude/skills/` & `.agents/skills/`):**
   - `clean-room-rebuild`: Quy trình xây dựng lại tính năng từ `SPEC.md` có bằng chứng xác thực.
   - `qa-7-layers`: Quy trình kiểm thử 7 tầng bắt buộc trước khi xuất xưởng.
   - `mcp-5-layers`: Khuôn mẫu thiết kế MCP Server 5 lớp bảo mật.
   - `docker-3-skills`: Tối ưu hóa Docker multi-stage (giảm 90% dung lượng), Docker Compose và Container Registry.

---

## 3. Quy Trình Làm Việc Bắt Buộc Trong Mọi Phiên (Session Protocol)

1. **Trước khi viết code:**
   - Kiểm tra [`docs/ROADMAP.md`](./docs/ROADMAP.md) (hoặc [`docs/ops/HANDOFF.md`](./docs/ops/HANDOFF.md) nếu là phiên nhận bàn giao) để xác định đúng tính năng thuộc Sprint hiện tại.
   - Đối chiếu quy tắc nghiệp vụ tại [`docs/product/BUSINESS_FLOW.md`](./docs/product/BUSINESS_FLOW.md) và thiết kế tại [`docs/design/DESIGN.md`](./docs/design/DESIGN.md).
2. **Trong khi viết code:**
   - Tách nhánh Git riêng (`feat/...`, `fix/...`) theo [`docs/ops/GIT_WORKFLOW.md`](./docs/ops/GIT_WORKFLOW.md). Tuyệt đối không đẩy thẳng vào nhánh `main`/`production`.
   - Mỗi tệp mã nguồn giữ dưới **500 dòng**, viết đầy đủ 100% không dùng bình luận giữ chỗ (`// TODO`, `// ...`).
3. **Sau khi viết code (Pre-Flight Check):**
   - Chạy kiểm tra kiểu tĩnh (`tsc --noEmit` / linter) và bộ kiểm thử liên quan trong [`docs/qa/QA_TESTING.md`](./docs/qa/QA_TESTING.md).
   - Cập nhật trạng thái tính năng vào [`docs/ROADMAP.md`](./docs/ROADMAP.md), chữ ký kiến trúc vào [`docs/technical/ARCHITECTURE_PLAN.md`](./docs/technical/ARCHITECTURE_PLAN.md) và lịch sử vào [`docs/CHANGELOG.md`](./docs/CHANGELOG.md).
