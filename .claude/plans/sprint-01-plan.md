# SPRINT 01 TECHNICAL PLAN — Nền Móng Hệ Thống & Xác Thực Bảo Mật

> Tệp này nằm trong `.claude/plans/`, được sinh ra từ `docs/product/BUSINESS_FLOW.md` và `docs/ROADMAP.md` trước khi AI bắt đầu viết code cho từng Sprint.

---

## 1. Mục Tiêu Sprint 01
Xây dựng nền móng Monorepo (`frontend/`, `backend/`, `infra/`, `mcp/`) và hoàn thiện cụm tính năng **Xác thực JWT + Refresh Token + Redis Blacklist + Phân quyền RBAC** (`[FEAT-001]` $\rightarrow$ `[FEAT-005]`).

---

## 2. Danh Sách User Stories Thực Thi Trong Sprint

| Mã | User Story | Tiêu chí nghiệm thu (Acceptance Criteria) |
| :--- | :--- | :--- |
| `US-01` | Là **Guest**, tôi muốn đăng ký và đăng nhập bằng Email/Mật khẩu để truy cập hệ thống. | Mật khẩu băm bằng Argon2/Bcrypt; trả về cặp `access_token` (15m) và `refresh_token` (7d). |
| `US-02` | Là **System**, tôi muốn khóa tạm thời tài khoản nhập sai mật khẩu quá 5 lần liên tiếp trong 15 phút. | Bộ đếm lưu trên Redis với TTL 900s; trả về HTTP `429 Too Many Requests` ở lần thử thứ 6. |
| `US-03` | Là **User/Admin**, khi tôi bấm Đăng xuất hoặc đổi mật khẩu, mọi token cũ phải bị vô hiệu hóa ngay lập tức. | Đưa `jti` của token vào `Redis Blacklist` với TTL bằng thời gian sống còn lại của token. |
| `US-04` | Là **Admin**, tôi muốn phân quyền chi tiết theo vai trò (`STUDENT`, `INSTRUCTOR`, `ADMIN`) trên từng nhóm API. | Middleware RBAC chặn chính xác 100% truy cập trái phép với HTTP `403 Forbidden`. |

---

## 3. Phân Bổ Tệp Cần Tạo Mới / Chỉnh Sửa (Giới Hạn `< 500 Dòng/Tệp`)

```text
infra/
├── docker-compose.yml                        # Khởi tạo PostgreSQL, Redis, MinIO, Nginx Gateway
└── postgres/migrations/001_init_auth.sql     # Bảng users, roles, permissions, audit_logs

backend/
├── src/config/env.schema.ts                  # Kiểm tra biến môi trường Fail-Fast bằng Zod
├── src/modules/auth/auth.controller.ts       # Các route /login, /refresh, /logout
├── src/modules/auth/auth.service.ts          # Xử lý JWT, Refresh Token & Redis Blacklist
├── src/modules/auth/rbac.guard.ts            # Middleware kiểm tra quyền RBAC
└── tests/auth.integration.spec.ts            # Kiểm thử tích hợp & nghiệp vụ (Tầng 1, 2, 3)

frontend/user/
├── src/components/ui/                        # Button, Input (font >= 16px), Skeleton, ErrorState
└── src/features/auth/components/LoginForm.tsx # Form đăng nhập chuẩn Mobile-First (4 trạng thái UI)
```
