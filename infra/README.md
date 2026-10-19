# Infrastructure & DevOps Workspace (`infra/`)

Thư mục này chứa toàn bộ cấu hình hạ tầng, đóng gói container và kịch bản triển khai.

```text
infra/
├── docker/             # Multi-stage Dockerfiles cho frontend, backend, mcp
├── compose/            # docker-compose.dev.yml & docker-compose.prod.yml
├── nginx/              # Reverse proxy, SSL termination, Rate limiting & Security headers
└── scripts/            # Kịch bản backup DB, healthcheck & zero-downtime rollout
```

## Quy Chuẩn Bắt Buộc Cho AI Agent Khi Cấu Hình Hạ Tầng
1. **3 Kỹ Năng Docker Bắt Buộc (`.claude/skills/docker-3-skills/SKILL.md`):**
   - Multi-Stage Build tối ưu dung lượng image.
   - Chạy bằng Non-Root User (`USER appuser`) và khai báo `HEALTHCHECK` rõ ràng.
   - Biến môi trường nhạy cảm truyền qua Runtime Secret, tuyệt đối không `ARG`/`ENV` cứng trong `Dockerfile`.
2. **Permission Gate (`.claude/rules/permission-gate.md`):**
   - Không tự ý thay đổi cấu hình mạng/port production hoặc xóa volume dữ liệu khi chưa có sự phê duyệt của con người.
