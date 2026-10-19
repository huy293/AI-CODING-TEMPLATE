# CHANGELOG — Lịch Sử Phiên Bản & Điểm Khôi Phục (Semantic Versioning)

> **Quy tắc quản lý phiên bản:**
> - Đánh nhãn phiên bản độc lập hoặc đồng bộ theo chuẩn Semantic Versioning (`MAJOR.MINOR.PATCH`: `v1.0.0`, `v1.1.0`, `v1.1.1`).
> - Mỗi phiên bản gắn rõ phân khu thay đổi (`[frontend]`, `[backend]`, `[infra]`, `[mcp]`, `[docs]`) và mã Git Tag để có thể **Rollback tức thì trong 1 lệnh** khi xảy ra sự cố trên môi trường vận hành.

---

## [`v0.2.0`] — 2026-10-20 (Current Sprint 02 Baseline)

### Added (`Tính năng mới`)
- **`[docs]`**: Hoàn thiện toàn bộ hệ điều hành tài liệu `.md` theo 6 bước Vibe Coding (`ROADMAP.md`, `BUSINESS_FLOW.md`, `AUDIT_BLUEPRINT.md`, `visual-spec.html`, `DESIGN.md`, `ARCHITECTURE_PLAN.md` 27 mục, `FEATURE_MCP.md`, `SPEC.md` Clean-Room, `QA_TESTING.md` 7 tầng, `PENTEST_REPORT.md`, `HANDOFF.md` 6 mục, `GIT_WORKFLOW.md`, `CICD_PIPELINE.md`, `OBSERVABILITY.md`).
- **`[.claude & .agents]`**: Thiết lập đủ 6 thư mục điều khiển AI (`rules/`, `commands/`, `skills/`, `hooks/`, `plans/`, `references/`).

---

## [`v0.1.0`] — 2026-10-19 (Sprint 01 Foundation)

### Added (`Tính năng nền móng`)
- **`[infra]`**: Cấu trúc mạng nội bộ Docker Compose cho PostgreSQL, Redis, MinIO và Nginx API Gateway.
- **`[backend]`**: Đặc tả và kiểm thử luồng xác thực `JWT + Refresh Token + Redis Blacklist` (`[FEAT-001]` $\rightarrow$ `[FEAT-005]`).
- **`[frontend]`**: Khởi tạo phân khu `landing-page/`, `user/`, `admin/` theo chuẩn Mobile-First.

### Rollback Command Reference (`Lệnh khôi phục khẩn cấp`)
```bash
# Khôi phục toàn bộ mã nguồn hoặc từng phân khu về phiên bản ổn định v0.1.0:
git checkout v0.1.0 -- backend/ frontend/
```
