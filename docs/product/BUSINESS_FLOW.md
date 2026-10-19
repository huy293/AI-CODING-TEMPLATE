# BUSINESS FLOW SPECIFICATION (`BUSINESS_FLOW.md`)

> **Vai trò của tệp này:** Đây là khuôn mẫu biến các gạch đầu dòng ý tưởng thô thành **Đặc tả Quy trình Nghiệp vụ chuẩn mực**.
> - **Dev Agent** đọc tệp này khi chạy `/build` để viết logic không sót trường hợp biên.
> - **QA Testing Engineer Agent** đọc tệp này để chạy **Kiểm thử Tầng 3 (Business Flow Test)**.

---

## Quy Trình 01: Xác Thực Người Dùng & Quản Lý Phiên An Toàn

| Thuộc tính | Nội dung đặc tả |
| :--- | :--- |
| **Mã quy trình** | `BF-AUTH-01` |
| **Tên quy trình** | Đăng nhập, Làm mới Token & Thu hồi phiên tức thì (`JWT + Refresh Token + Redis Blacklist`) |
| **Tác nhân (Actors)** | - `Guest` (Người dùng chưa đăng nhập)<br/>- `User / Student` (Học viên / Khách hàng)<br/>- `Instructor` (Giảng viên / Nhân sự vận hành)<br/>- `Admin` (Quản trị viên cao nhất)<br/>- `System` (Backend Auth Service + PostgreSQL + Redis) |
| **Phân khu liên quan** | `frontend/user/`, `frontend/admin/`, `backend/src/modules/auth/`, `infra/redis/` |

### 1. Luồng Sự Kiện Chính (Main Success Scenario)
1. **Actor (`Guest`)** mở trang đăng nhập, nhập `email` và `password` rồi nhấn nút **Đăng nhập**.
2. **System (`Backend`)** kiểm tra bộ đếm giới hạn thử sai trên Redis (`ratelimit:login:<email_or_ip>`).
3. **System** truy vấn người dùng trong PostgreSQL, đối chiếu mật khẩu băm (`Argon2id`/`Bcrypt`) và kiểm tra trạng thái tài khoản (`is_active = true`, `deleted_at IS NULL`).
4. Nếu hợp lệ, **System** xóa bộ đếm thử sai trên Redis, sinh cặp khóa:
   - `access_token` (JWT chứa `sub`, `role`, `jti`, thời hạn `15 phút`).
   - `refresh_token` (Thời hạn `7 ngày`, lưu định danh phiên vào Redis).
5. **System** ghi nhận bản ghi vào bảng `audit_logs` (chuẩn 13 trường) và điều hướng Actor vào đúng phân khu theo vai trò (`frontend/user` hoặc `frontend/admin`).

### 2. Quy Tắc Nghiệp Vụ & Ngoại Lệ (Business Rules — Bắt Buộc Kiểm Thử Ở Tầng 3)
- **`BR-AUTH-01` (Tiêu chuẩn mật khẩu):** Mật khẩu bắt buộc tối thiểu 8 ký tự, bao gồm chữ hoa, chữ thường, chữ số và ký tự đặc biệt.
- **`BR-AUTH-02` (Chống dò mật khẩu Brute-force):** Nếu nhập sai mật khẩu liên tiếp **5 lần**, khóa tạm thời tính năng đăng nhập của tài khoản/IP đó trên Redis trong **15 phút** (`HTTP 429 Too Many Requests`).
- **`BR-AUTH-03` (Thu hồi phiên tức thì qua Redis Blacklist):** Khi Actor bấm **Đăng xuất**, đổi mật khẩu, hoặc bị **Admin** khóa tài khoản, mã `jti` của `access_token` hiện tại và `refresh_token` bị đưa ngay vào `Redis Blacklist` để chặn mọi request tiếp theo trong mili-giây.

---

## Quy Trình 02: Thanh Toán Đơn Hàng & Xử Lý Webhook IPN Chống Trùng Lặp

| Thuộc tính | Nội dung đặc tả |
| :--- | :--- |
| **Mã quy trình** | `BF-PAY-02` |
| **Tên quy trình** | Khởi tạo đơn hàng, Thanh toán qua cổng (MoMo / VNPay / VietQR) & Đối soát Webhook IPN |
| **Tác nhân (Actors)** | - `User` (Người mua khóa học / dịch vụ)<br/>- `Payment Gateway` (Hệ thống cổng thanh toán gọi Webhook IPN)<br/>- `Admin / Accountant` (Kế toán đối soát)<br/>- `System` (Backend Payment Service + PostgreSQL + Redis) |
| **Phân khu liên quan** | `frontend/user/`, `frontend/admin/`, `backend/src/modules/payment/`, `infra/postgres/` |

### 1. Luồng Sự Kiện Chính (Main Success Scenario)
1. **Actor (`User`)** chọn gói khóa học/sản phẩm, áp dụng mã giảm giá (nếu có) và nhấn **Thanh toán**.
2. **System (`Backend`)** tính toán tổng tiền tại máy chủ (tuyệt đối không tin giá tiền gửi lên từ trình duyệt), tạo bản ghi đơn hàng ở trạng thái `PENDING` và sinh chữ ký bảo mật `HMAC-SHA256`.
3. **System** chuyển hướng hoặc hiển thị mã QR thanh toán cho **Actor (`User`)**.
4. Khi khách thanh toán xong, **Actor (`Payment Gateway`)** gọi ngầm vào cổng Webhook `/api/v1/payments/ipn`.
5. **System** xác thực chữ ký `HMAC-SHA256`, lấy khóa phân tán trên Redis (`SETNX payment:lock:<order_id>`), cập nhật trạng thái đơn sang `PAID`, kích hoạt quyền truy cập khóa học cho `User` trong một Database Transaction duy nhất (`ACID`).

### 2. Quy Tắc Nghiệp Vụ & Ngoại Lệ (Business Rules)
- **`BR-PAY-01` (Chống giả mạo số tiền & chữ ký):** Nếu chữ ký `HMAC-SHA256` hoặc tổng tiền trong gói tin IPN lệch dù chỉ `1 VNĐ` so với đơn gốc trong DB, từ chối ngay (`HTTP 400`) và phát cảnh báo bảo mật về Telegram.
- **`BR-PAY-02` (Chống cộng tiền/kích hoạt 2 lần - Idempotency):** Nếu cổng thanh toán gửi trùng lặp 2–5 gói tin IPN cùng lúc cho 1 đơn hàng đã `PAID`, hệ thống chỉ xử lý đúng lần đầu tiên và trả kết quả đã ghi nhận cho các request trùng sau đó.
- **`BR-PAY-03` (Xử lý đơn treo quá hạn):** Đơn hàng ở trạng thái `PENDING` quá **15 phút** không thanh toán sẽ tự động chuyển sang `EXPIRED` và hoàn trả lại số lượng mã giảm giá đã giữ chỗ.

---

## Quy Trình 03: Tải Lên Tài Liệu & Media An Toàn (MinIO / Cloudflare R2)

| Thuộc tính | Nội dung đặc tả |
| :--- | :--- |
| **Mã quy trình** | `BF-MEDIA-03` |
| **Tên quy trình** | Upload tệp tin đa phương tiện qua Presigned URL & Phòng chống Path Traversal |
| **Tác nhân (Actors)** | `Instructor`, `User`, `Admin`, `System` (Backend + MinIO/R2) |

### Quy Tắc Nghiệp Vụ (Business Rules)
- **`BR-MEDIA-01` (Loại bỏ hoàn toàn tên tệp gốc từ người dùng):** Cấm nối trực tiếp tên file do người dùng gửi lên vào đường dẫn lưu trữ (chống lỗ hổng **Path Traversal** `../../etc/passwd`). Bắt buộc đổi tên tệp sang `UUIDv7` kết hợp kiểm tra Magic Bytes thực tế của tệp.
- **`BR-MEDIA-02` (Giới hạn dung lượng & định dạng):** Ảnh tối đa `5MB` (`webp`, `png`, `jpg`), tài liệu PDF tối đa `25MB`.
