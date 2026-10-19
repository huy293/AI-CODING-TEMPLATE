# PROJECT ROADMAP & SPRINT TRACKER (`ROADMAP.md`)

> **Quy tắc vận hành bắt buộc (`.claude/rules/roadmap-signature.md`):**
> 1. AI chỉ thực hiện tối đa **1 Sprint (5–10 tính năng)** mỗi đợt, sau đó cập nhật trạng thái vào tệp này và chờ con người xác nhận (`Confirm`) trước khi chuyển sang Sprint tiếp theo.
> 2. Tuyệt đối không dùng `/loop` chạy tràn lan 100 tính năng một lúc gây đốt token và bỏ sót tính năng.
> 3. Mọi tính năng hoàn thành phải gắn mã phiên bản (`vX.Y.Z`) đồng bộ với [`docs/CHANGELOG.md`](./CHANGELOG.md).

---

## 1. Tiến Độ Tổng Thể (Overall Progress)

| Chỉ số | Giá trị hiện tại |
| :--- | :--- |
| **Phiên bản hiện tại (Current Version)** | `v0.2.0` |
| **Sprint đang thực thi** | `Sprint 02 — Thanh toán, Phân quyền RBAC & Lưu trữ Media` |
| **Tổng số tính năng đã hoàn thành** | `5 / 20 tính năng (25.0%)` |
| **Trạng thái kiểm thử QA 7 tầng** | `Sprint 01: PASS 100%` · `Sprint 02: IN_PROGRESS` |

---

## 2. Việc Còn Lại Cần Làm (Remaining Backlog — Các Sprint Tiếp Theo)

### Sprint 03 — Giám Sát Hệ Thống, MCP Server 5 Lớp & Bảo Mật Nâng Cao
- [ ] `[FEAT-011]` Xây dựng MCP Server độc lập trong `mcp/` với 5 lớp bảo mật (API Key, Rate Limit, RBAC Scope, Tool Registry, Scoped Client).
- [ ] `[FEAT-012]` Tích hợp bộ phản xạ JEV chấm điểm rủi ro (`0.00–1.00`) cho các thao tác ghi dữ liệu.
- [ ] `[FEAT-013]` Chuẩn hóa bản ghi Log 13 trường và kết nối Prometheus + Grafana + Loki + Tempo cảnh báo về Telegram.
- [ ] `[FEAT-014]` Thiết lập CDC Pipeline đồng bộ dữ liệu và sao lưu tự động PostgreSQL + Redis (RDB & AOF).
- [ ] `[FEAT-015]` Thực thi quy trình 7 bước Pentest nội bộ và quét lỗ hổng thư viện bên thứ ba.

### Sprint 04 — Tối Ưu Hóa Docker, CI/CD 8 Bước & Triển Khai Zero-Downtime
- [ ] `[FEAT-016]` Tối ưu Multi-stage Dockerfile cắt giảm 90% dung lượng Image cho `frontend/`, `backend/`, `mcp/`.
- [ ] `[FEAT-017]` Cấu hình Nginx API Gateway & cô lập mạng nội bộ Docker (`internal_net`).
- [ ] `[FEAT-018]` Thiết lập GitHub Actions CI/CD 8 bước (Dev tự động, Production yêu cầu bấm Approve PR).
- [ ] `[FEAT-019]` Triển khai chiến lược Zero-Downtime Deployment & kiểm thử tự phục hồi (Reliability Test Tầng 7).
- [ ] `[FEAT-020]` Tối ưu hóa chi phí AI Gateway (Prompt Caching, Model Routing, Batch API).

---

## 3. Thứ Tự Ưu Tiên Trong Sprint Hiện Tại (Current Sprint 02 Priority Matrix)

| Thứ tự | Mã tính năng | Tên tính năng | Phân khu (`Folder`) | Độ ưu tiên | Trạng thái |
| :---: | :--- | :--- | :--- | :---: | :--- |
| 1 | `FEAT-006` | Phân quyền chi tiết RBAC (`STUDENT`, `INSTRUCTOR`, `ADMIN`) | `backend/` + `frontend/admin/` | `P0 - Critical` | `IN_PROGRESS` |
| 2 | `FEAT-007` | Tích hợp cổng thanh toán Webhook IPN chống cộng tiền trùng (Redis Idempotency) | `backend/` + `infra/redis/` | `P0 - Critical` | `TODO` |
| 3 | `FEAT-008` | Upload tài liệu & video qua MinIO / Cloudflare R2 (Chống Path Traversal) | `backend/` + `infra/minio/` | `P1 - High` | `TODO` |
| 4 | `FEAT-009` | Giao diện Quản lý khóa học & Thanh toán chuẩn Mobile-First (4 trạng thái UI) | `frontend/user/` | `P1 - High` | `TODO` |
| 5 | `FEAT-010` | Bảng điều khiển đối soát doanh thu & Quản lý người dùng cho Admin | `frontend/admin/` | `P2 - Medium` | `TODO` |

---

## 4. Tính Năng Mới Bổ Sung (New Feature Requests / Change Requests)

> *Mọi yêu cầu phát sinh thêm từ người dùng trong quá trình phát triển phải được AI ghi vào đây trước khi xếp vào Sprint:*

- [ ] `[CR-001]` Bổ sung tính năng xuất hóa đơn PDF tự động sau khi thanh toán thành công *(Ngày thêm: 2026-10-20 — Dự kiến đưa vào Sprint 03)*.
- [ ] `[CR-002]` Bổ sung thông báo nhắc lịch học qua Zalo/Telegram *(Ngày thêm: 2026-10-20 — Dự kiến đưa vào Sprint 04)*.

---

## 5. Đã Hoàn Thành & Sẵn Sàng Kiểm Thử (Completed & Verified — Sprint 01)

- [x] `[FEAT-001]` Khởi tạo kiến trúc Monorepo (`frontend/`, `backend/`, `infra/`, `mcp/`, `docs/`) & hệ thống điều khiển `.claude/` (`v0.1.0`).
- [x] `[FEAT-002]` Kiểm tra biến môi trường tập trung Fail-Fast bằng Zod Schema khi khởi động ứng dụng (`v0.1.0`).
- [x] `[FEAT-003]` Hệ thống Đăng nhập & Xác thực JWT + Refresh Token (`v0.1.0`).
- [x] `[FEAT-004]` Cơ chế thu hồi phiên tức thì bằng `Redis Blacklist` khi đăng xuất hoặc khóa tài khoản (`v0.1.0`).
- [x] `[FEAT-005]` Chống Brute-force đăng nhập: Khóa tạm thời 15 phút khi nhập sai mật khẩu quá 5 lần (`v0.1.0` — *Đã pass QA Tầng 1, 2, 3, 4*).
