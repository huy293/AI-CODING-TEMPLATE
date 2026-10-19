---
trigger: always_on
description: Quy hoạch cấu trúc thư mục Monorepo và quy tắc lưu trữ tài liệu .md trong docs/
---

# RULE 01: FOLDER STRUCTURE & MODULE ISOLATION (LUÔN KÍCH HOẠT)

## 1. Phân Định Ranh Giới 5 Phân Khu Cốt Lõi
Mọi đoạn mã và tài liệu do AI tạo ra bắt buộc đặt đúng phân khu:
1. **`frontend/`**: Chứa giao diện người dùng, tách biệt rõ 3 phân hệ:
   - `frontend/landing-page/`: Trang giới thiệu, blog, tối ưu SEO/SSR.
   - `frontend/user/`: Ứng dụng chính phục vụ khách hàng/người dùng cuối.
   - `frontend/admin/`: Hệ thống quản trị nội bộ, thống kê, phân quyền RBAC.
2. **`backend/`**: Chứa API Server, xác thực, xử lý nghiệp vụ (Business Logic). Tuyệt đối không lưu file upload hay trạng thái phiên trực tiếp trên ổ cứng container.
3. **`infra/`**: Chứa cấu hình hạ tầng (`docker-compose.yml`, Nginx Gateway, PostgreSQL migrations/backups, Redis, MinIO, Prometheus/Grafana/Loki/Tempo).
4. **`mcp/`**: Chứa MCP Server & MCP Gateway độc lập (5 lớp bảo mật) để kết nối AI với dữ liệu hệ thống.
5. **`docs/`**: Kho lưu trữ toàn bộ tài liệu `.md` của dự án.

## 2. Kỷ Luật Lưu Trữ Tài Liệu `.md` (Chống Rác Thư Mục Gốc)
- **CẤM** tạo file `.md` mới tràn lan ở thư mục gốc (`root`), ngoại trừ `README.md`, `AGENTS.md`, `CLAUDE.md`.
- Khi xuất bất kỳ tài liệu nào, AI bắt buộc xếp đúng vào 6 phân khu trong `docs/`:
  - Tiến độ & Phiên bản: `docs/ROADMAP.md`, `docs/CHANGELOG.md`
  - Nghiệp vụ & Sản phẩm: `docs/product/` (`BUSINESS_FLOW.md`, `AUDIT_BLUEPRINT.md`, `visual-spec.html`)
  - Thiết kế UI/UX: `docs/design/` (`DESIGN.md`)
  - Kỹ thuật & Kiến trúc: `docs/technical/` (`ARCHITECTURE_PLAN.md`, `FEATURE_MCP.md`)
  - Đặc tả Clean-Room: `docs/specs/` (`SPEC.md`)
  - Kiểm thử & Bảo mật: `docs/qa/` (`QA_TESTING.md`, `PENTEST_REPORT.md`)
  - Vận hành, Git, CI/CD & Chuyển giao: `docs/ops/` (`HANDOFF.md`, `GIT_WORKFLOW.md`, `CICD_PIPELINE.md`, `OBSERVABILITY.md`)

## 3. Giới Hạn Độ Dài File & Kỷ Luật Phạm Vi
- Mỗi file code tối đa **500 dòng**. Vượt ngưỡng bắt buộc tách module theo nguyên lý Single Responsibility (SRP).
- Yêu cầu sửa tính năng/component nào thì chỉ can thiệp đúng file/hàm đó (Function-Level Isolation). Cấm tự ý refactor các file lân cận không liên quan.
