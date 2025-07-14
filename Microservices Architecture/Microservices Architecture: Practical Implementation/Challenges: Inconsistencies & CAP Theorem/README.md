**Thách thức: Tính không nhất quán & Định lý CAP**

Trong bài học này, chúng ta sẽ thảo luận về một số thách thức có thể phát sinh khi sử dụng microservices bất đồng bộ.

---

### Sự kiện cũ

Nếu cơ sở hạ tầng truyền thông cho việc truyền sự kiện cần lưu trữ các sự kiện cũ, nó phải xử lý một lượng lớn dữ liệu. Do đó, nếu các sự kiện cũ bị thiếu, trạng thái của các microservice không còn có thể được tái cấu trúc từ các sự kiện.

Là một biện pháp tối ưu, có thể không cần thiết phải lưu **các sự kiện không còn liên quan**. Nếu một khách hàng đã chuyển địa chỉ nhiều lần, địa chỉ cuối cùng có thể là địa chỉ duy nhất quan trọng. Các địa chỉ khác có thể bị xóa.

Ngoài ra, hệ thống cũng phải có khả năng **tiếp tục xử lý các sự kiện cũ**. Nếu sơ đồ dữ liệu của sự kiện thay đổi trong lúc đó, **các sự kiện cũ cần được di chuyển**. Nếu không, mọi microservice phải có khả năng xử lý các sự kiện ở mọi định dạng cũ.

Điều này đặc biệt khó khăn nếu dữ liệu mới nằm trong các sự kiện chưa được lưu vào ổ đĩa.

---

### Tính không nhất quán

Do truyền thông bất đồng bộ, hệ thống không nhất quán. Một số microservice đã có thông tin nhất định, trong khi các microservice khác thì không.

Ví dụ, **lệnh xử lý** có thể đã có thông tin về một đơn hàng, nhưng **lệnh xuất hóa đơn** hoặc **vận chuyển** thì chưa biết về thứ tự đơn hàng.

**Vấn đề này không thể được giải quyết.** Mất một khoảng thời gian để truyền thông bất đồng bộ đạt được sự đồng thuận giữa các hệ thống.

---

### Định lý CAP

Tính không nhất quán không chỉ là vấn đề thực tiễn mà **không thể giải quyết về mặt lý thuyết**.

Theo [định lý CAP](https://en.wikipedia.org/wiki/CAP_theorem), có ba đặc điểm tồn tại trong một hệ thống phân tán:

* **Tính nhất quán (Consistency)**: Tất cả các thành phần của hệ thống đều có cùng một thông tin.
* **Tính sẵn sàng (Availability)**: Hệ thống phản hồi các yêu cầu ngay cả khi có lỗi xảy ra.
* **Khả năng chịu phân vùng (Partition tolerance)**: Hệ thống vẫn tiếp tục hoạt động ngay cả khi xảy ra mất kết nối giữa các phần của mạng.

> Định lý CAP phát biểu rằng hệ thống chỉ có thể chọn **hai trong ba đặc tính** trên.

Phân vùng mạng là trường hợp đặc biệt. Một hệ thống phải phản hồi ngay cả khi mất gói dữ liệu. Trên thực tế, không cần mất kết nối hoàn toàn – việc mất gói hoặc phản hồi chậm cũng có thể gây ra hiện tượng giống như một hệ thống bị lỗi hoàn toàn.

---

### Lý do cho Định lý CAP

Hình minh họa cho thấy khi xảy ra phân vùng mạng, một hệ thống có thể:

1. **Xử lý yêu cầu bằng cách cung cấp phản hồi không nhất quán**: Đây là trường hợp **AP**. Hệ thống vẫn sẵn sàng, nhưng có thể trả về dữ liệu lỗi thời.
2. **Từ chối yêu cầu và không phản hồi**: Đây là trường hợp **CP**. Hệ thống không sẵn sàng nhưng vẫn duy trì tính nhất quán.

---

### Thỏa hiệp với CAP

Tất nhiên, bạn có thể đưa ra thỏa hiệp. Hãy xem **hệ thống với năm bản sao**:

* Khi ghi, mỗi bản sao xác nhận rằng dữ liệu đã được ghi thành công.
* Khi đọc, hệ thống có thể đọc từ các bản sao để lấy phiên bản mới nhất.

Hệ thống như vậy sẽ ưu tiên **tính sẵn sàng**. Việc mất một hoặc hai nút không làm hệ thống dừng lại.

Tuy nhiên, điều này **không đảm bảo tính nhất quán cao**:

* Dữ liệu có thể chỉ được ghi vào một nút, và do độ trễ trong việc sao chép, các bản sao khác vẫn chưa có dữ liệu mới → dữ liệu cũ vẫn được đọc ra.

Hệ thống có thể yêu cầu ít nhất năm nút xác nhận mới ghi/đọc dữ liệu, nhưng khi đó, **sự cố mất một nút** cũng có thể khiến hệ thống **không sẵn sàng**.

---

### CAP, sự kiện, và nhân bản dữ liệu

Định lý CAP cũng áp dụng cho cơ sở dữ liệu NoSQL, vốn đạt hiệu suất và độ tin cậy cao qua việc nhân bản. Nhưng điều tương tự cũng xảy ra với các hệ thống sử dụng sự kiện hoặc nhân bản dữ liệu.

Ví dụ, một sự kiện có thể được xem như một hình thức nhân bản dữ liệu giữa các microservice. Tuy nhiên, **mỗi microservice có thể phản ứng khác nhau với sự kiện và chỉ sử dụng một phần dữ liệu**.

> Một kiến trúc microservice dựa trên truyền thông bất đồng bộ, sự kiện và nhân bản dữ liệu tương ứng với **một hệ thống AP**.

Microservice có thể xử lý yêu cầu bằng cách sử dụng dữ liệu cục bộ, và vẫn sẵn sàng ngay cả khi các hệ thống khác gặp lỗi.

> Định lý CAP nói rằng **phương án duy nhất còn lại là hệ thống CP**. Hệ thống này nhất quán nhưng **không sẵn sàng**.

Ví dụ: nếu lưu dữ liệu trong một microservice trung tâm, tất cả các microservice khác có thể truy cập và nhận được dữ liệu mới nhất. Tuy nhiên, **nếu microservice trung tâm gặp sự cố, các microservice khác sẽ không còn hoạt động được.**
