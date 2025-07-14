### **Sao chép dữ liệu, Bounded Context và Giao thức**

---

#### **Sao chép dữ liệu và Bounded Context**

Giao tiếp bất đồng bộ trở nên phức tạp khi cần dữ liệu để thực thi yêu cầu.

Ví dụ, hệ thống catalog, quy trình đặt hàng và hóa đơn đều cần dữ liệu về sản phẩm và khách hàng.

* Mỗi hệ thống lưu trữ một phần thông tin của các đối tượng nghiệp vụ.

  * Catalog cần hình ảnh và mô tả sản phẩm.
  * Hóa đơn cần giá và thuế.

Điều này phản ánh **bounded context**:

* Mỗi bounded context có mô hình miền riêng, dữ liệu đặc thù được lưu trong cơ sở dữ liệu riêng biệt của nó.
* Các bounded context khác không được truy cập trực tiếp vào dữ liệu này để đảm bảo tính đóng gói.
* Thay vào đó, dữ liệu được truy cập thông qua logic và giao diện của bounded context đó.

Nếu toàn bộ dữ liệu của sản phẩm được chứa trong một hệ thống duy nhất, mô hình sẽ rất phức tạp và gây chặt coupling giữa các hệ thống.

Một hệ thống thứ ba như đăng ký khách hàng hoặc quản lý danh mục sản phẩm sẽ nhận dữ liệu và chuyển các phần cần thiết đến các hệ thống khác, có thể làm bất đồng bộ.

Các bounded context khác lưu trữ dữ liệu cục bộ thông qua sự kiện, ví dụ sự kiện "sản phẩm mới được thêm" khiến các bounded context thêm dữ liệu vào mô hình miền của mình.

Đăng ký hoặc danh mục sau khi gửi dữ liệu đi sẽ không cần lưu trữ lại.

Có thể dùng cách thức Extract-Transform-Load (ETL) để tải dữ liệu ban đầu hoặc khôi phục dữ liệu khi có sự không nhất quán.

---

#### **Giao thức giao tiếp đồng bộ**

* Đòi hỏi server phản hồi cho từng yêu cầu.
* Ví dụ: REST, HTTP — mỗi request nhận response kèm mã trạng thái và dữ liệu.
* Có thể dùng giao thức đồng bộ để thực hiện giao tiếp bất đồng bộ (được giải thích kỹ ở chương 8).

---

#### **Giao thức giao tiếp bất đồng bộ**

* Tự nhiên hơn khi dùng giao thức bất đồng bộ, gửi tin nhắn mà không cần chờ phản hồi.
* Ví dụ: hệ thống messaging như Kafka (xem chương 7).
* Cả REST và messaging đều có thể dùng cho giao tiếp đồng bộ (request/reply) hoặc bất đồng bộ (fire & forget, event).
