
---

### **Định nghĩa**

Bài học này giới thiệu về **microservices bất đồng bộ**.

* Microservice **đồng bộ** (synchronous) là microservice gửi yêu cầu tới các microservices khác trong quá trình xử lý và **chờ kết quả** trả về.
* Microservice **bất đồng bộ** (asynchronous) là microservice mà:

  * (a) Không gửi yêu cầu đến microservices khác trong quá trình xử lý yêu cầu, hoặc
  * (b) Gửi yêu cầu đến microservices khác nhưng **không chờ kết quả trả về**.

---

### Hai trường hợp của microservice bất đồng bộ

1. **Không giao tiếp trong quá trình xử lý yêu cầu**

   Microservice không giao tiếp với hệ thống khác khi đang xử lý yêu cầu, mà giao tiếp ở thời điểm khác. Ví dụ, microservice có thể sao chép dữ liệu cần thiết để sử dụng khi xử lý, như dữ liệu khách hàng được lưu cục bộ thay vì mỗi lần xử lý đều phải gọi đến hệ thống khác.

2. **Giao tiếp nhưng không chờ phản hồi**

   Microservice gửi yêu cầu tới microservice khác nhưng không đợi phản hồi. Ví dụ, microservice xử lý đơn hàng gửi yêu cầu tạo hóa đơn đến microservice hóa đơn mà không cần chờ kết quả để tiếp tục.

---

### Ví dụ kiến trúc bất đồng bộ phức tạp

Trong hệ thống thương mại điện tử:

* Khách hàng chọn hàng trong catalog.
* Quy trình đặt hàng tạo đơn hàng.
* Hóa đơn và dữ liệu vận chuyển được tạo ra.
* Microservice đăng ký thêm khách hàng mới.
* Microservice quản lý danh mục hàng hóa.

---

### Giao tiếp bất đồng bộ không cần phản hồi

* Catalog thu thập hàng hóa trong giỏ, khi khách hàng đặt hàng, giỏ hàng được gửi sang quy trình đặt hàng.
* Quy trình đặt hàng chuyển giỏ hàng thành đơn hàng.
* Đơn hàng trở thành hóa đơn và đơn giao hàng.

Các bước này có thể thực hiện bất đồng bộ, không cần dữ liệu phản hồi quay lại; trách nhiệm xử lý đơn được chuyển lần lượt cho các bước tiếp theo.

---

Bạn muốn mình giúp tóm tắt ngắn hơn hoặc giải thích thêm phần nào không?
