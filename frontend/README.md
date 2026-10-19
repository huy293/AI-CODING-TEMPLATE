# Frontend Workspace (3-Module Architecture)

Thư mục này chứa 3 ứng dụng Frontend độc lập trong kiến trúc Monorepo chuẩn Vibe Coding:

```text
frontend/
├── landing-page/       # 1. Trang giới thiệu & chuyển đổi khách hàng (SEO-first, Static/SSR)
├── user/               # 2. Ứng dụng chính dành cho người dùng cuối (App Portal)
└── admin/              # 3. Hệ thống quản trị nội bộ & phân quyền RBAC (Backoffice)
```

## Quy Chuẩn Bắt Buộc Cho AI Agent Khi Code Frontend
1. **Ranh Giới File (Scope Isolation):** Khi thực hiện task thuộc `frontend/`, AI Agent chỉ được phép đọc/ghi trong `frontend/` và đọc contract từ `docs/design/DESIGN.md` + `docs/technical/ARCHITECTURE_PLAN.md`.
2. **Mobile-First & Touch UX:**
   - Thiết kế cho viewport `360px - 430px` trước, mở rộng lên Desktop bằng `md:` và `lg:`.
   - Nút bấm tối thiểu `44x44px`, font-size ô input tối thiểu `16px` (`text-base`).
   - Sử dụng `<button type="button">` và `<a>`/`<Link>`, tuyệt đối cấm `<div onClick>`.
3. **Đủ 4 Trạng Thái Giao Diện:** Mọi component bất đồng bộ phải có `Loading` (Skeleton), `Error` (Retry), `Empty` (CTA), và `Success`.
4. **Giới Hạn File:** Không vượt quá `500 dòng/file`. Page Orchestrator (`page.tsx`) tối đa `50-80 dòng`.
