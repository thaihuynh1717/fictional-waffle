## 🚀 **Varnish – Giải pháp Caching & ESI cho Web**

### 🧾 **Giới thiệu**

* **Varnish** là một **web cache** hiệu suất cao, thường được dùng để tăng tốc các máy chủ web.
* Nó cũng là một **ESI (Edge Side Includes) engine** giúp tích hợp phía máy chủ.

---

### ⚙️ **Cách hoạt động của Varnish**

* **Chặn các yêu cầu HTTP** từ người dùng → **kiểm tra cache**.
* Nếu có sẵn trong cache → **trả về kết quả ngay**.
* Nếu **không có**, chuyển tiếp yêu cầu đến web server và lưu kết quả vào cache.

⏱️ **Hiệu quả**: Tăng hiệu suất và giảm tải cho server backend.

---

### 🔒 **Giấy phép & Hỗ trợ**

* Varnish có **giấy phép BSD**.
* Được phát triển và hỗ trợ thương mại bởi **Varnish Software**.

---

### 📦 **Docker và Cấu hình**

* Trong ví dụ, Varnish chạy trong **container Docker** (Ubuntu 14.04 LTS).
* Cấu hình nằm ở `docker/varnish/default.vcl`.

#### Một số điểm chính trong cấu hình:

* `vcl 4.0`: Chọn phiên bản VCL 4.
* `backend default`: Trỏ đến microservice **order**.
* `backend common`: Chứa phần dùng chung như **header**, **footer**, **Bootstrap UI**.

---

### 📥 **Xử lý yêu cầu & phản hồi**

* `vcl_recv`: Kiểm tra đường dẫn (VD: `/common/...`) để chuyển hướng đến backend phù hợp.
* `vcl_backend_response`: Cấu hình caching & ESI:

  * `beresp.do_esi = true`: Kích hoạt **xử lý thẻ ESI** trong HTML.
  * `beresp.ttl = 30s`: **Cache trong 30 giây** → đơn giản nhưng hiệu quả.
  * `beresp.grace = 15m`: Nếu backend **bị lỗi**, vẫn có thể trả dữ liệu đã được cache trước đó.

💡 **Lưu ý**: Cache theo thời gian đơn giản nhưng **có thể bỏ sót dữ liệu mới**. Invalidate cache thủ công sẽ phức tạp hơn trong hệ thống lớn.

---

### 🔁 **Load Balancing**

* Có thể cấu hình **load balancing** trong VCL bằng cách khai báo nhiều `backend`.
* Hoặc dùng một **load balancer ngoài** → để Varnish chỉ lo caching & ESI.

---

### 🛠️ **Sức mạnh của VCL**

* VCL (Varnish Configuration Language) rất mạnh, giúp:

  * **Tùy biến request/response** theo điều kiện cụ thể.
  * Loại bỏ **cookies** để hợp lệ hóa cache.
  * Áp dụng chính sách cache phức tạp.

📚 **Tài liệu tham khảo**: [Varnish Book](https://book.varnish-software.com/) (miễn phí, chi tiết).

---

### ✅ **Tóm tắt lợi ích**

| Lợi ích              | Mô tả                                                       |
| -------------------- | ----------------------------------------------------------- |
| ⚡ Tăng hiệu suất     | Cache và trả lại dữ liệu nhanh hơn                          |
| 🧩 Hỗ trợ ESI        | Ghép các phần giao diện từ nhiều microservices              |
| 🔁 Xử lý lỗi backend | Dùng cache trong thời gian backend tạm thời không hoạt động |
| 🧠 Tùy biến mạnh mẽ  | Nhờ ngôn ngữ VCL                                            |

---

Bạn có muốn mình giúp minh họa cách cấu hình Varnish với ví dụ cụ thể không?
