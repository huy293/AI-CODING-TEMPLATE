# Custom MCP Server Workspace (`mcp/`)

Thư mục này chứa mã nguồn **Custom Model Context Protocol (MCP) Server** theo kiến trúc 5 lớp chuẩn hóa (`.claude/skills/mcp-5-layers/SKILL.md` & `docs/technical/FEATURE_MCP.md`), giúp các AI Agent (Claude Code, Antigravity, Cursor) truy vấn trực tiếp ngữ cảnh dự án mà không cần đọc toàn bộ mã nguồn gây tốn token.

```text
mcp/
├── src/
│   ├── transport/      # Lớp 1: Stdio / Streamable HTTP Transport
│   ├── router/         # Lớp 2: Tool, Resource & Prompt Registry
│   ├── validator/      # Lớp 3: Zod Input Schema & RBAC Permission Gate
│   ├── adapters/       # Lớp 4: Read-only DB Inspector, OpenAPI & Roadmap Reader
│   └── sanitizer/      # Lớp 5: PII Masking & Token-Guard Compactor
└── README.md
```

## Quy Chuẩn Bắt Buộc Cho AI Agent Khi Phát Triển MCP
1. **Read-Only By Default:** Các tool tra cứu DB/Schema chỉ được dùng tài khoản Read-Only, tự động chặn các câu lệnh `INSERT`, `UPDATE`, `DELETE`, `DROP`, `ALTER`.
2. **Token Budget Guard:** Mọi kết quả trả về từ MCP Tool phải giới hạn kích thước (phân trang hoặc tóm tắt `< 2000 tokens/call`) và che giấu toàn bộ thông tin nhạy cảm (`password`, `token`, `secret`, `apiKey`).
