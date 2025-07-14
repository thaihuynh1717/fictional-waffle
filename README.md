### **Resource-oriented Client Architecture (ROCA)**

---

**ROCA** là một kiến trúc xây dựng ứng dụng web theo hướng tài nguyên (resource-oriented), phù hợp để **modular hóa** và **tích hợp giao diện người dùng (UI)** trong hệ thống phân tán như Self-contained Systems (SCSs).

---

### 🌐 **Nguyên tắc chính của ROCA**

#### ✅ Tuân thủ nguyên tắc REST:

* Mỗi tài nguyên có một **URL riêng biệt**.
* URL có thể chia sẻ qua email, mở từ mọi trình duyệt (nếu được cấp quyền).
* Dùng **phương thức HTTP đúng cách** (GET không thay đổi dữ liệu).
* **Server không lưu trạng thái (stateless)**.

#### 📄 Dữ liệu dễ truy cập:

* Ngoài HTML, tài nguyên có thể được trả về dưới dạng **JSON, XML**, phục vụ cho ứng dụng khác ngoài trình duyệt.

#### 🧠 Logic nằm ở server:

* Mọi **logic nghiệp vụ** được xử lý ở phía **server**.
* JavaScript chỉ dùng để **cải thiện UI**.
* Giúp bảo mật hơn và dễ bảo trì.

#### 🔐 Xác thực thông qua HTTP:

* **Thông tin xác thực** (cookies, basic auth, client cert, v.v.) được gửi trong mỗi request.
* Không cần dùng **session server-side** để lưu trạng thái xác thực.

#### 🍪 Cookie chỉ dùng cho:

* Xác thực, theo dõi, chống CSRF.
* **Không được** chứa dữ liệu nghiệp vụ.

#### ❌ Không dùng session phía server:

* Đảm bảo tính **stateless**.
* Tăng khả năng chịu lỗi và cân bằng tải dễ hơn.

#### 🔄 Hỗ trợ chức năng chuẩn của trình duyệt:

* Các nút **Back, Forward, Refresh** phải hoạt động đúng – điều mà SPA thường gặp khó khăn.

#### 💅 HTML không chứa thông tin layout:

* HTML chỉ chứa nội dung, còn **giao diện được điều khiển bởi CSS**.
* Giúp hỗ trợ **screen reader** và người dùng khuyết tật.

#### ✨ JavaScript chỉ để nâng cao trải nghiệm:

* **Ứng dụng vẫn phải hoạt động được nếu không có JS** (dù không tối ưu).
* Không phụ thuộc hoàn toàn vào JS như SPA.

#### 🔁 Không lặp lại logic:

* **Không triển khai logic giống nhau ở cả client và server**.

---

### ✅ **Tóm lại**, ROCA hướng tới:

* Xây dựng ứng dụng web **chuẩn mực**, dễ bảo trì.
* Tận dụng tốt các nguyên lý web (HTML, CSS, HTTP).
* Thích hợp với **kiến trúc phân tán**, đặc biệt khi cần **tích hợp UI từ nhiều hệ thống**.

---

