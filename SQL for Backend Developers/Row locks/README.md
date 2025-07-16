Trong thiết kế ứng dụng, chúng ta cần xem xét khả năng mở rộng, tức là muốn ứng dụng xử lý đồng thời nhiều yêu cầu từ người dùng. Khi các giao dịch chạy đồng thời, kết quả có thể bị xung đột nếu không có cơ chế kiểm soát đồng thời (concurrency control). Một trong những cách triển khai cơ chế kiểm soát này là sử dụng **locks** trên tài nguyên của cơ sở dữ liệu (như một dòng, một vùng dữ liệu, hoặc một bảng).

🛢️ **SQL Lock**

Hiểu về database locking là điều cần thiết để lập trình một ứng dụng có thể xử lý đồng thời nhiều truy cập. Lock giúp duy trì tính toàn vẹn dữ liệu bằng cách chỉ cho phép một giao dịch duy nhất sửa đổi một đối tượng trong cơ sở dữ liệu tại một thời điểm.

Có hai loại lock chính: **Exclusive lock** và **Shared lock**.

---

### Shared (S) lock

🔓 S

Shared lock cho phép chia sẻ tài nguyên giữa các giao dịch đồng thời. Nó còn được gọi là **read lock**. Nhiều giao dịch đang đọc dữ liệu có thể cùng lúc có được shared lock để tránh chặn lẫn nhau, miễn là không có writer nào cần exclusive lock. Không giao dịch nào được phép sửa đổi tài nguyên khi shared lock đang tồn tại.

---

### Exclusive (X) lock

🔒 X

Exclusive lock được dùng khi cần sửa đổi tài nguyên một cách độc lập. Nó còn được gọi là **write lock**. Exclusive lock ngăn không cho tài nguyên được chia sẻ. Giao dịch đầu tiên có được exclusive lock sẽ là giao dịch duy nhất có thể sửa đổi tài nguyên cho đến khi lock được giải phóng.

💡 **Lưu ý**: Nhiều shared lock có thể được cấp cho cùng một tài nguyên tại một thời điểm. Nhưng nếu tài nguyên đã bị lock bởi exclusive lock, các quá trình khác sẽ không thể nhận shared hay exclusive lock nữa.

---

### Implicit lock

Locks có thể được cấp ngầm định (implicit) hoặc rõ ràng (explicit). Implicit lock được hệ quản trị cơ sở dữ liệu (DBMS) tự động xử lý, còn explicit lock thì do lập trình viên tự viết lệnh.

Khi xử lý một giao dịch, cơ sở dữ liệu sẽ tự động cấp các lock phù hợp khi thực thi DML/DDL. Câu lệnh đọc sẽ nhận shared lock (read lock); câu lệnh ghi sẽ nhận exclusive lock (write lock). DBMS sẽ tự động giải phóng lock sau khi giao dịch kết thúc.

Tùy vào DBMS, cơ chế lock có thể khác nhau, nhưng để đảm bảo tính chất ACID, cần nhớ rằng:

1. Câu lệnh DML thường yêu cầu implicit exclusive lock trên dòng hoặc vùng dữ liệu được sửa và shared lock trên bảng.
2. Câu lệnh DDL thường yêu cầu implicit exclusive lock trên bảng.

---

### Explicit lock

Một số giao dịch, do tính chất đặc biệt, cần đặt trước tài nguyên để tránh thay đổi từ các giao dịch khác. Giao dịch có thể cấp explicit lock để giữ dữ liệu và chiếm quyền exclusive cho đến khi commit hoặc rollback. Explicit lock sẽ ghi đè implicit lock mặc định.

Trong MySQL, cú pháp để cấp explicit lock là:

```sql
LOCK TABLES car WRITE;
```

---

### Deadlock

Deadlock xảy ra khi hai giao dịch chờ lock từ nhau và không giao dịch nào giải phóng lock trước.

Ví dụ:

* Transaction 1 giữ lock trên bảng `car`, chờ lock bảng `driver`.
* Transaction 2 giữ lock bảng `driver`, chờ lock bảng `car`.

Cả hai giao dịch đều không thể tiếp tục, dẫn đến **deadlock**. DBMS buộc phải hủy một trong hai giao dịch để giải quyết tình trạng này.

---

### Kết luận

Hãy nhớ rằng lock có thể tiếp tục tồn tại ngay cả sau khi câu lệnh đã hoàn thành. Một giao dịch có thể bận với các hoạt động khác trong khi vẫn giữ lock — điều này dễ dẫn đến deadlock.

Ví dụ, một ứng dụng yêu cầu người dùng nhập dữ liệu trong quá trình giao dịch sẽ giữ lock trong thời gian dài. Để tránh điều này, bạn nên:

* Giải phóng lock sớm,
* Commit hoặc rollback sớm,
* Tách logic thành nhiều giao dịch nhỏ.

---
