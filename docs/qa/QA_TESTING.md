# 7-LAYER QA TESTING MATRIX (`QA_TESTING.md`)

> **Vai trò của tệp này:** Dành riêng cho phiên **QA Testing Engineer Agent** (tách biệt với Dev Agent).
> - Khi Dev Agent báo "Đã code xong", QA Agent lắng nghe sự kiện và chạy đủ **7 tầng kiểm thử** dưới đây trước khi cho phép deploy.
> - **Nguyên tắc sắt đá:** QA Agent chỉ được viết test và báo lỗi, **CẤM** tự ý sửa code trong `frontend/` hoặc `backend/` để ép bài test vượt qua.

---

## 1. Bảng Tổng Hợp Trạng Thái 7 Tầng Kiểm Thử

| Tầng | Tên tầng kiểm thử | Công cụ thực thi | Đối tượng kiểm tra | Tỷ lệ đạt yêu cầu | Trạng thái hiện tại |
| :---: | :--- | :--- | :--- | :---: | :---: |
| **1** | **Source / Unit Test** | Vitest / Jest / Pytest | Từng hàm/method riêng lẻ (Input/Output, `null`, `[]`, biên số học). | `100% Pass` | `READY` |
| **2** | **Integration Test** | Supertest + Testcontainers | Kết nối thực tế giữa Frontend $\leftrightarrow$ Backend $\leftrightarrow$ PostgreSQL $\leftrightarrow$ Redis. | `100% Pass` | `READY` |
| **3** | **Business Flow Test** | Automated Spec Runner | Đối chiếu từng quy tắc `BR-XXX` trong [`docs/product/BUSINESS_FLOW.md`](../product/BUSINESS_FLOW.md). | `100% Pass` | `READY` |
| **4** | **End-to-End (E2E) Test** | Playwright (Mobile & Desktop) | Giả lập người dùng thật từ Đăng nhập $\rightarrow$ Mua hàng $\rightarrow$ Thanh toán. | `100% Pass` | `READY` |
| **5** | **Performance & Load Test** | k6 / Autocannon | Chịu tải nhiều user truy cập đồng thời (`p95 < 300ms`, `0% Error`). | `p95 < 300ms` | `READY` |
| **6** | **Security & Pentest** | OWASP Check + `npm audit` | Kiểm tra phân quyền RBAC, IDOR, Path Traversal, SQLi, XSS, CVE. | `0 Critical/High` | `READY` |
| **7** | **Reliability & Recovery** | Chaos / Fault Injection | Tự phục hồi khi DB/Redis ngắt kết nối đột ngột & khôi phục từ bản sao lưu. | `Auto-Recover` | `READY` |

---

## 2. Chi Tiết Kịch Bản Kiểm Thử Theo Từng Tầng

### Tầng 1: Source / Unit Test (Chuẩn Google Testing)
- Kiểm tra 4 điểm mù bắt buộc cho mọi hàm xử lý logic:
  1. Dữ liệu rỗng (`null`, `undefined`, chuỗi rỗng `""`, mảng rỗng `[]`).
  2. Giá trị biên (số `0`, số âm `-1`, số cực lớn `Number.MAX_SAFE_INTEGER`).
  3. Định dạng thời gian chuẩn UTC ISO-8601 (đuôi `Z`).
  4. Chuỗi chứa ký tự đặc biệt / mã độc (`<script>`, `' OR 1=1 --`, `../../etc/passwd`).

### Tầng 2: Integration Test (Kiểm Thử Tích Hợp Liên Tầng)
- Khởi chạy PostgreSQL và Redis thật trong môi trường kiểm thử cô lập.
- Xác minh: Khi gọi API `/api/v1/auth/logout`, khóa `jti` thực sự xuất hiện trong Redis và lần gọi API tiếp theo bị từ chối ngay ở tầng Middleware.

### Tầng 3: Business Flow Test (Đối Chiếu Nghiệp Vụ `BUSINESS_FLOW.md`)
- **`[BF-AUTH-01 / BR-AUTH-02]`:** Gửi liên tiếp 5 request đăng nhập sai mật khẩu $\rightarrow$ Kiểm tra request thứ 6 trả về đúng `HTTP 429 Too Many Requests`.
- **`[BF-PAY-02 / BR-PAY-02]`:** Bắn đồng thời 5 gói tin Webhook IPN trùng mã đơn hàng $\rightarrow$ Kiểm tra số dư khóa học chỉ được kích hoạt đúng 1 lần duy nhất.

### Tầng 4: End-to-End (E2E) Playwright (Kiểm Thử Đầu Cuối Đa Thiết Bị)
- **Kiểm tra Mobile trước tiên (`375x812` & `390x844`):**
  - Đảm bảo không có thanh cuộn ngang (`scrollWidth <= clientWidth`).
  - Mọi nút bấm chính có vùng chạm $\ge 44\times 44\text{px}$.
- **Kiểm tra Desktop (`1440x900`):**
  - Chạy trọn vẹn luồng người dùng từ trang chủ $\rightarrow$ đăng nhập $\rightarrow$ đặt hàng $\rightarrow$ xác nhận đơn.

### Tầng 5: Performance & Load Test (Kiểm Thử Chịu Tải)
- Giả lập 100–500 người dùng truy cập đồng thời trong 60 giây vào các API trọng tâm.
- Phát hiện sớm lỗi truy vấn `N+1` hoặc thiếu Index trong PostgreSQL.

### Tầng 6: Security & Pentest (Kiểm Thử Bảo Mật)
- Thực thi theo [`docs/qa/PENTEST_REPORT.md`](./PENTEST_REPORT.md): thử dùng Token của `STUDENT` gọi vào API của `ADMIN` để xác nhận bị chặn `403 Forbidden`.

### Tầng 7: Reliability & Recovery Test (Kiểm Thử Độ Tin Cậy & Phục Hồi)
- Tạm dừng container `redis` hoặc `postgres` trong 5 giây rồi bật lại $\rightarrow$ Xác nhận `backend` tự động kết nối lại (Auto-reconnect) mà không bị sập tiến trình (`CrashLoopBackOff`).
