
### Hiểu về Chỉ mục Cơ sở Dữ liệu - Được giải thích bằng ví dụ

Là một quy tắc chung, thời gian truy vấn tăng lên khi dữ liệu tăng. Hầu hết các hệ quản trị cơ sở dữ liệu hiện đại cung cấp một tính năng đặc biệt để xử lý vấn đề này – **chỉ mục**. Chỉ mục giúp tăng tốc đáng kể các thao tác tìm kiếm trên các bảng cơ sở dữ liệu.

Trong bài viết này, bạn sẽ tìm hiểu về các loại chỉ mục phổ biến trong cơ sở dữ liệu và cách chúng được triển khai.

---

### Một phép ẩn dụ đời thực

Trước khi bắt đầu với chỉ mục cơ sở dữ liệu, hãy xem một phép ẩn dụ. Hãy tưởng tượng một thư viện với hàng nghìn cuốn sách. Chỉ mục là danh sách các cuốn sách được sắp xếp theo các tiêu chí khác nhau (ví dụ: tên tác giả, tiêu đề, năm phát hành). Nếu bạn muốn tìm một cuốn sách cụ thể, bạn sẽ tìm trong chỉ mục chứ không phải xem từng cuốn một.

Tương tự như vậy, chỉ mục cơ sở dữ liệu lưu trữ dữ liệu theo một cách dễ tìm kiếm hơn. Các chỉ mục giúp truy xuất nhanh chóng bằng cách duyệt qua một tập hợp con của dữ liệu thay vì toàn bộ bảng.

---

### Chỉ mục trong cơ sở dữ liệu

Theo mặc định, các hệ quản trị cơ sở dữ liệu không tạo chỉ mục trừ khi bạn chỉ rõ chúng. Chỉ mục là các cấu trúc dữ liệu đặc biệt giúp tìm kiếm hiệu quả hơn. Tuy nhiên, có sự đánh đổi – chúng chiếm dung lượng bộ nhớ bổ sung và làm chậm các thao tác ghi như `INSERT`, `UPDATE`, hoặc `DELETE`.

> 📌 **Lưu ý:** Càng nhiều chỉ mục, càng tốn tài nguyên. PostgreSQL, MySQL, SQLite và các hệ quản trị khác đều cho phép tạo chỉ mục thủ công, vì vậy bạn nên chỉ tạo khi cần thiết, dựa trên truy vấn thực tế.

---

### Phân loại chỉ mục

Dựa theo PostgreSQL và MySQL (các hệ thống phổ biến), có nhiều loại chỉ mục khác nhau. Dưới đây là một số loại chỉ mục chính được phân loại theo:

#### ➤ Chỉ mục đơn giản

Chỉ mục đơn giản chỉ bao gồm một cột. Ví dụ: `CREATE INDEX idx_name ON users(name);` sẽ tạo chỉ mục trên cột `name`.

#### ➤ Chỉ mục phức hợp

Chứa nhiều hơn một cột (ví dụ: `CREATE INDEX idx_comp ON users(first_name, last_name);`) và cho phép các truy vấn theo nhiều cột được tối ưu hóa tốt hơn.

#### ➤ Chỉ mục cụm và không cụm

* **Cụm (clustered index):** Sắp xếp dữ liệu vật lý trên đĩa theo thứ tự của chỉ mục. Mỗi bảng chỉ có một chỉ mục dạng cụm.
* **Không cụm (non-clustered):** Lưu trữ con trỏ tới vị trí dữ liệu thực tế.

---

### Phân loại theo cấu trúc dữ liệu

Theo cấu trúc dữ liệu, các chỉ mục được chia thành bốn loại phổ biến:

* **B-Tree (Balanced Tree)**
* **Hash**
* **Bitmap**
* **Spatial (Không gian)**

Chúng ta sẽ tìm hiểu chi tiết hai loại quan trọng nhất là **B-Tree** và **Hash**.

---

### B-Tree Index

Đây là loại phổ biến nhất. Khi bạn không chỉ rõ loại chỉ mục, hệ quản trị mặc định sẽ tạo chỉ mục dạng B-Tree.

#### ➤ Khi nào dùng B-Tree?

* Khi cần sắp xếp dữ liệu (`ORDER BY`)
* Khi truy vấn với so sánh (`=`, `<`, `>`, `BETWEEN`, `IN`)
* Khi lọc dữ liệu theo khoảng

> B-Tree là cấu trúc cây nhị phân cân bằng. Nó lưu trữ giá trị theo thứ tự và duy trì thứ tự đó. Do vậy, tìm kiếm trên B-Tree rất hiệu quả, thường là O(log n).

---

### Hash Index

Chỉ mục dạng Hash dựa trên hàm băm. Nó ánh xạ một giá trị đầu vào tới một giá trị băm cố định. Được sử dụng chủ yếu cho các truy vấn có điều kiện bằng (`=`).

#### Ưu điểm:

* Truy vấn bằng `=` nhanh hơn B-Tree
* Ít bộ nhớ hơn
* Tránh việc phải duyệt nhiều node như B-Tree

#### Nhược điểm:

* Không hỗ trợ so sánh `<`, `>`, `BETWEEN`, `ORDER BY`
* Không hỗ trợ tìm kiếm theo khoảng
* Ít phổ biến hơn trong các hệ quản trị

---

### Các loại chỉ mục khác

* **Bitmap Index:** Tốt cho các cột có số lượng giá trị riêng biệt thấp (nhị phân, boolean).
* **Giản đồ đảo (inverted index):** Dùng trong hệ thống tìm kiếm văn bản.
* **Chỉ mục không gian:** Dùng cho dữ liệu địa lý.

---

### Kết luận

Chỉ mục trong cơ sở dữ liệu rất quan trọng để tăng hiệu suất truy vấn. Dưới đây là tóm tắt:

* **B-Tree:** Đa năng nhất, hỗ trợ nhiều loại truy vấn.
* **Hash:** Hiệu quả với truy vấn `=`, nhưng không hỗ trợ các phép so sánh khác.
* **Bitmap & Inverted:** Tối ưu cho dữ liệu đặc biệt như boolean hoặc văn bản.

Việc chọn đúng chỉ mục phụ thuộc vào loại dữ liệu và loại truy vấn bạn thực hiện thường xuyên.


