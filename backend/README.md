# Backend Workspace (Modular Service Architecture)

Thư mục này chứa toàn bộ mã nguồn Backend API, Business Logic, Database Schema/Migrations và Queue Workers.

```text
backend/
├── src/
│   ├── modules/        # Chia theo Domain/Feature (auth, billing, users, notifications)
│   ├── common/         # Middlewares, Guards, Interceptors, Error Filters, Logger 13 trường
│   ├── config/         # Fail-fast Zod Environment Validation
│   └── database/       # Prisma/Drizzle Schema, Migrations & Seeders
└── tests/              # Unit Test, Integration Test & Pentest scripts
```

## Quy Chuẩn Bắt Buộc Cho AI Agent Khi Code Backend
1. **Kiến Trúc 3 Lớp Rõ Ràng:** `Controller / Route` (nhận request, validate DTO) $\rightarrow$ `Service` (xử lý nghiệp vụ, Transaction) $\rightarrow$ `Repository / DAO` (truy xuất DB).
2. **Strict Typing & Zero Silent Catch:**
   - Validate 100% request body/query/params bằng `Zod` hoặc `class-validator`.
   - Cấm `catch (e) {}` rỗng; mọi lỗi phải đi qua Global Exception Filter và ghi log chuẩn 13 trường (`docs/ops/OBSERVABILITY.md`).
3. **An Toàn Cơ Sở Dữ Liệu:**
   - Thời gian lưu trữ chuẩn **UTC ISO-8601 (đuôi `Z`)**.
   - Áp dụng Soft Delete (`deleted_at`) và Audit Log cho dữ liệu quan trọng.
   - Tuân thủ Expand & Contract Migration, cấm chạy lệnh xóa DB (`reset`, `push --force`).
